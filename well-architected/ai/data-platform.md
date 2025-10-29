---
title: Data Platform for AI Workloads on Azure
description: Learn about functional and nonfunctional data source considerations for your data platform for AI workloads on Azure.
author: PageWriter-MSFT
ms.author: prwilk
ms.date: 11/01/2024
ms.topic: concept-article
ms.update-cycle: 180-days  
---

# Data platform for AI workloads on Azure

A data platform is an integrated set of technologies that are designed to manage workload requirements by ingesting source data and then filtering, aggregating, and preparing it for consumption.

Data has distinct characteristics that are based on its intended use. We highly recommend that you understand the principles of good data pipeline design before you explore the technological capabilities that this article describes. For more information, see [Training data design](./training-data-design.md) and [Grounding data design](./grounding-data-design.md).

The platform also fulfills storage needs when data rests at certain points in the pipeline. If the workload is complex and handles large-scale data, then you can distribute pipeline tasks among various components. For simpler use cases, evaluate whether you can use the source data in a store that offers those combined capabilities.

Ask yourself the following questions so that you can avoid designing an overly complex architecture for your data platform. It's always best to keep things simple when you can.

- Can your application have the expected predictive power by ingesting data from a single source?
- Does your initial choice of data store support data warehousing capabilities?
- Is the source data already optimized for AI searches?

If you answer yes to these questions, then you can simplify your architecture by allowing the application to access the data source directly. This approach eliminates the need for big data architecture components like data ingestion, analytical store integration, and external data processing. If the source database can handle the required searches, integrating the search index capability directly into the source database can be a practical approach. Make sure that the source can cost-effectively scale to meet new demands.

For instance, Azure Cosmos DB and Azure SQL Databases support vector search, so you might not need another index. Built-in support for vector data types eliminates the need for separate vector databases reducing architectural complexity and data movement while improving query performance for machine learning operations. Another use case is to use read replicas as endpoints for search operations. For SQL databases that have read replicas, direct searches to these replicas can optimize performance. 


A data platform architecture for large-scale workloads is more complex.

Ingesting data from multiple data sources and orchestrating searches across various platforms can become complex and inefficient. Also, you still need some extract, transform, and load (ETL); extract, load, and transform (ELT); or extract and load (EL) processes to reshape the data within the data store. The scenario becomes more complex as the data requires more processing. You need to add many components to the architecture to handle the end-to-end pipeline from ingestion to serving queries. Many big data technologies are highly specialized and built to handle those processing tasks effectively.

One such technology is the search index. The primary advantage of adding a separate index is its ability to efficiently manage queries and process large volumes of data that have high throughput. This function offloads AI capabilities from the original data source so that the index can focus on its main function, serving queries.

Choose a platform based on its specific functionality and purpose, and consider your functional and technical requirements. If your architecture is evolving to handle complex use cases, focus on the following sections about aggregated data stores, processing pipelines, and search indexes.

## Recommendations

Here's the summary of recommendations provided in this article.

|Recommendation|Description|
|---|---|
|**Build secure, performant, and cost-effective data stores**. | A key part of your data platform is a data store that aggregates data from multiple sources and allows integration with various integration tasks. This helps your workload perform at scale. Be sure to review the various functional and non-functional requirements of your data store to ensure a cost effective deployment. <br><br>&#9642; [Considerations for storing aggregated data](#considerations-for-processing-data)|
|**Follow best practices for data ingestion and processing**. | High quality data helps improve the reliability of your workload and the end user experience. Consider the requirements of your workload as well as key best practices to build efficient ingestion and data transition processes that help maintain a high quality bar. <br><br>&#9642; [Considerations for processing data](#considerations-for-processing-data)|
|**Design reliable and relevant search indexes**. | Aim for a high-performing, write-once, read-many data store that efficiently handles impromptu and fuzzy queries, delivering relevant results to your user base, even when queries aren't precise. <br><br>&#9642; [Considerations for a search index](#considerations-for-a-search-index)|
|**Ensure functional data stores perform at scale**.  | Depending on the functional requirements of your workload you might need to create functional data stores, for example for offline inferencing. It's important that you create data stores with their designated function in mind and apply best practices for the function. <br><br>&#9642; [Considerations for a feature store](#considerations-for-a-feature-store)<br>&#9642; [Considerations for an offline inferencing data store](#considerations-for-an-offline-inferencing-data-store)|

## Considerations for storing aggregated data

In AI workloads, data moves through various stages of storage and processing with the help of pipelines that orchestrate workflow in between these stages. One key stage is a data store that contains data that's ingested and aggregated from multiple sources. You need this store to conduct processing until the data reaches a suitable state for training or indexing. The primary focus is on ensuring that the data accurately reflects its source.

> [!NOTE]
> An alternative approach is to directly access data sources. However, this approach can lead to performance problems because it might overload the source systems with AI features. There can also be data access problems. To avoid these problems, we recommend that you copy data to this store.

The data platform for this store should meet the security standards applied at data sources, be cost effective, and support integration with ETL, ELT, and EL processing tasks. Options vary from basic storage to big data technologies based on data volume. Choose economical storage that helps you achieve enough reliability and performance.

The following section provides guidance about the capabilities to consider when you select a data store technology. For more information, see [Data processing pipelines](#considerations-for-processing-data).

### Functional requirements

- **Can the platform handle various data formats?**

  The data store should be able to store various data formats and transform them to other formats if necessary.
  
  Suppose that your ingestion pipeline sources data from a relational database and a Parquet file, so it supports both structured and semistructured data. You want to convert relational data to Parquet format in accordance with its schema definitions. The data platform should have built-in capabilities to do that transformation without you writing custom code.

- **Do you expect to store multiple versions of the data?**

  Data values and schemas can change over time, and managing multiple versions of the data becomes important. 

  Source systems typically store only current data, not historical data. If it's important to retain historical data, you might need to duplicate large data sets from source systems. In this case, versioning can disambiguate current data from historical data. 

  In some cases, you might need to maintain copies of data for different use cases. To support this scenario, you might need to fork data. Each fork can independently mutate to enhance its quality and usability. Your data platform should be able to maintain proper versioning of those forks.

  Your data platform should be able to store versions of data over time to provide historical context. This context is beneficial for processing and training AI models because it offers multiple observations rather than just a single point in time.

- **Does the platform have built-in data lifecycle management capabilities?**

  Data lifecycle management (DLM) is a process for managing data from its creation to its deletion, with stages such as data collection, storage, usage, archiving, and disposal.

  Without DLM, data can grow uncontrollably, often resulting in multiple copies as it moves through quality tiers. The data platform should have DLM capabilities to prevent unbounded data growth.

  Consider this scenario. The preprocessing step needs to repeat to refine the data until it reaches an acceptable quality for training purposes. Your data platform should be able to delete intermediate copies of the data.

  In some cases, you might need to retain data for regulatory audits. The data platform should have cold storage capabilities for infrequently accessed data so that you can archive it at a lower cost.

- **Does the platform support data governance features?**

  Auditability is an important aspect for AI workloads. The data store should maintain audit trails that can track data access, ensure privacy, and understand data origins.

  Use a data dictionary feature to you manage metadata, data types, purposes, and lineage. This feature is especially important when data is ingested from multiple sources.

- **Do you plan to conduct training with production data?** 

  There are two approaches to deployments, model deployment and code deployment. In model deployment, production data is used in development, which requires stringent security measures. In code deployment, the model doesn't see production data until it's in production. Although code deployment simplifies security concerns in the development environment, it can increase compute costs. Whichever approach you choose, your data platform should support separate environments for development and production.  

- **Are you prioritizing convenience features over key functional features?**

  When you choose a data platform for AI or machine learning, don't rely just on its notebook capabilities. Although notebooks are useful for exploratory data analysis, they shouldn't be the deciding factor. Compute resources for notebooks are typically outside the scope of the aggregation data store. They're usually integrated with other resources, such as Azure Machine Learning.

### Nonfunctional requirements

- **How much data do you expect to store?**

  AI workloads generate a lot of data. Volume can increase significantly because of multiple versions and extra metadata. 
  
  Scalability for storage and throughput is important. The data platform must efficiently consume data from the ingestion pipeline while it handles data volume, manages concurrent writes, and ensures individual write performance without degradation. Those criteria also apply to the processing pipeline that reads, processes, and even writes back to the store.
  
  When you make a decision, consider the entire process because ingestion and processing often occur simultaneously. The design must be able to manage frequent data movement and processing. The data platform should offer high levels of parallelism to process data effectively.

  The platform technology should emit telemetry that gives meaningful insight into the throughput and performance of the read and write operations. 

- **Is this data store a critical component that contributes to the reliability target of the workload?**

  Choose a data store that enhances both reliability and scalability by using multiple instances. Big data stores often have a built-in controller that orchestrates data processing across instances. If one copy fails, then another can be used.

  Keep in mind that data doesn't serve its purpose if it isn't correct or accessible. The data platform should guarantee durability and make sure that the data remains intact. Make sure that the APIs that query the data are accessible. Additionally, consider data stores that have backup features.

  In general, you don't need to back up this data. However, if the cost of aggregating data each time from scratch is significantly high, you can consider rehydrating the data from a backup.

- **Do you have any cost constraints?**

  If data reliability and performance are sufficient, consider the cost impact. 

  The system should be optimized for _write once, read many_ to avoid overspending on data storage. The training or grounding data is important but not critical like a production database, which requires instant responsiveness. The focus is on balancing cost with just enough efficiency to maximize the return on investment.

The preceding requirements might naturally lead you to consider using a data lake because it offers DLM, quality tiers, observability, and support for diverse file formats. If your workload already uses a data lake, take advantage of that resource to meet your AI needs. Alternatively, you can choose other storage options, such as Azure Blob Storage, which provides some level of DLM, monitoring capabilities, and high transaction rates.

## Considerations for processing data

You must process data in the aggregate data store to increase its utility downstream. ETL pipelines perform this task, which is most important at the following points:

- **Ingestion layer**

  The pipeline is responsible for collecting data from various sources and moving it to the aggregate data store. During this process, the pipeline typically performs basic preprocessing and might even structure the data in a queryable format.
  
  To minimize the need for custom code, we recommend offloading much of this responsibility to a data platform. When you select a technology, consider the ETL characteristics required to support model training and augmentation.

- **Processing layer**

  Data from the aggregate data store undergoes extensive processing before it can be used for indexing or model training use cases. The processing pipeline requires levels of reliability and scaling that are similar to the ingestion pipeline. The main difference is the type of processing done on the data. 
  
  The process involves significant rescoping and restructuring of data. This process includes tasks like entity recognition, integrating additional data into the data set, and performing lookups. This process might also include deleting unnecessary data and applying data logic through a data orchestration platform.

The data processing stage can produce various outputs, which land in different destinations for different intents. Its main goal is to prepare and transfer data from the aggregated data store for consumption by the final destination. The consumer can either pull data when needed, or the processing layer can push data when it's ready.

> [!NOTE] 
> In the context of machine learning and generative AI, it's important to distinguish between ETL, ELT, and EL processes. Traditional ETL is crucial for data warehousing and object-relational mappings, where, because of schema restrictions, data must be transformed before you load it into the target system. ELT involves extracting data, loading it into a data lake, and then transforming it by using tools like Python or PySpark. In generative AI, particularly for retrieval-augmented generation (RAG), the process often involves extracting and loading documents to storage first, followed by transformations like chunking or image extraction.

The following section provides guidance to consider when you select a data processing technology that has ETL capabilities.
  
### Functional requirements

- **What's the support for connecting to data sources?**

  Data that needs to be processed might be stored in relational databases, big data sources, or various storage solutions.

  Most data processing technologies support prebuilt integrations that let you connect to various data sources without writing code. The connectors have features like the ability to copy data from source to sink, perform lookups, and apply some form of data governance. There are tools that offer drag-and-drop features to avoid unnecessary coding.

  Choose a data platform that makes it easy to integrate with the expected data sources.

- **Can the platform process various data formats?**

  Data might come in various formats, such as structured data like databases and JSON, unstructured data like images and documents, or streaming data like data from Internet of Things devices. The pipelines should be able to handle the expected file types.

- **Does the platform offer features for data preparation and rescoping?**

  You must process data that you intend to use for training or augmentation until it's suitable for training, fine-tuning, or indexing. Your data design strategies should explicitly outline the requirements.

  The following articles describe specific considerations: 

  - [Design training data for AI workloads on Azure](./training-data-design.md#data-preprocessing)
  - [Design grounding data for AI workloads on Azure](./grounding-data-design.md#data-preparation)
  
  As part of basic cleansing, the platform removes duplicates, fills in missing values, and eliminates extraneous noise during ingestion. For certain use cases, such as implementing a RAG pattern, we recommend that you lowercase chunks.

  Although these preprocessing steps are necessary, the platform must also support rich data manipulation that's specific to your needs. This process involves loading, rescoping, and transforming data. For certain models, the platform must be able to query external sources for document analysis, such as document intelligence or other AI tools. This work is needed for preparing the data and for data enrichment.

  If your data store supports this level of processing, you can localize this stage in the store without moving it elsewhere. Otherwise, you need an external technology such as Azure Databricks or Azure Data Factory. These technologies are suitable for moving data and performing manipulations, such as filtering, filling missing values, and standardizing string casing. For more complex tasks, a job-hosting platform is typically required. You can use Spark pools for big data orchestration.

  In certain use cases, you might want to externalize this responsibility to the consumer of the data. For example, AI models that use machine learning offer job processing capabilities to read, manipulate, and write data by using custom Python code.

  Another example is RAG implementation. A common processing step is chunking, where a document is divided into multiple chunks, and each chunk becomes a row in the index. It also stores embeddings, which an OpenAI service often generates, for these chunks. In AI searches, this process is orchestrated within the indexing workflow, whether by using OpenAI or Azure AI Search.
  
- **Is there a built-in orchestrator for managing workflows?**
  
  The processing tasks are modular and run as jobs. The platform should have orchestration capabilities that break down the workflow into steps or jobs. Each job should be independently defined, run, and monitored.

  In complex workflows, certain steps depend on the successful completion of previous ones. The orchestrator should handle job dependencies and make sure that tasks are completed in the correct order.

  Data design is an iterative process, so the orchestrator tool should be flexible enough to modify workflows easily. You should be able to inject new steps or adjust existing ones without rewriting large portions of code.

  Data Factory is a popular choice because it provides a rich feature set for managing data workflows. Azure Databricks can also manage complex workflows and schedule and monitor jobs. You should also consider the cost implications. For example, Azure Databricks features might be extensive, but they're also costly. An open-source alternative option, such as Apache NiFi, might be more cost effective.

  Ultimately, which tool you choose depends on what your organization allows and the skills that the workload team is comfortable with.

### Nonfunctional requirements

When you choose a processing pipeline, it's crucial to balance throughput and observability. The pipeline must reliably process and land the necessary data for models or indexes within a sufficient timeframe. It should be lightweight enough to support your current needs and be scalable for future growth. Teams must decide how much they need to future-proof the platform to avoid technical debt later. Key considerations include the frequency and volume of data ingestion, the reliability of the process, and the need for observability to monitor and address problems promptly.

- **How much data do you expect to ingest?**

  For the ingestion and processing stages, consider the platform's scalability and speed for handling tasks. For example, you expect to load 10 terabytes of data daily into an index or for model training. Your data ingestion platform should be able to process that much volume and with the expected throughput. In this case, using Azure Logic Apps might not be feasible because it could fail under such a load. Instead, Data Factory is better suited for this scale of data processing. 

  One way to handle high volume is through parallelism because it allows for more efficient data handling and processing. Platforms such as Azure Databricks can orchestrate tasks by creating multiple instances for the same job and distributing the load efficiently.

  Also, consider the tolerable latency and the complexity of the jobs. For instance, data cleansing involves validating and potentially replacing invalid fields or masking sensitive information. These tasks, though basic, require significant resources because each row is processed individually, which adds to the overall time.

- **What monitoring capabilities do you need?**

  Data processing pipelines should have monitoring capabilities and provide insights into the pipeline's performance and status of jobs.

  You must be able to track the progress of the jobs. Suppose the pipeline runs a data cleansing job that doesn't complete or completes partially. There might be downstream impact on the quality of data with which the model is trained, which might affect the predictive power.

  Similar to other components in the workload, you should enable logs, metrics, and alerts on the data pipeline to understand its behavior. Collect and analyze performance metrics to understand the efficiency and reliability aspects.

  Identify any gaps in the built-in telemetry, and determine what additional monitoring you need to implement. This monitoring might involve adding custom logging or metrics to capture specific details about the job steps.
  
- **How much reliability do you expect of the data processing platform?**

  The reliability of a data processing pipeline varies based on the choice of platform. Even though Logic Apps has orchestration capabilities, it might not be as reliable as Data Factory. Data Factory, hosted on an Azure Kubernetes Service (AKS) cluster, might have different reliability characteristics.
  
  Single-instance setups are considered points of failure. Choose a platform that supports reliability features, such as multiple instances, to meet your requirements.

  The platform should also support resiliency features. For example, the orchestrator should automatically retry a failed task, which reduces the need for manual restarts.

  Batch processing can be less reliable than inferencing, depending on data freshness and latency requirements. If training occurs weekly and processing takes one day, occasional failures are acceptable because there's enough time to retry.

- **Are there any cost constraints?**

  When you consider the cost-effectiveness of a data processing pipeline, it's important to choose a solution that meets your needs without unnecessary expenses. If your requirements don't justify the advanced features of Azure Databricks, a more economical option like Data Factory might be sufficient. Additionally, open-source tools like Apache Airflow or Apache NiFi can provide robust capabilities at a lower cost. The key is to avoid overspending on features that you don't need and select a platform that balances functionality and cost efficiency.

- **What are the security requirements on the workflows and on the data that you process?**

  Be clear about the security, privacy, and data residency requirements. For example, consider any geographic regulatory requirements. Comply with data residency requirements by ensuring that data is stored and processed within specific regions. You might need to run separate pipelines for different regions, such as one for Europe and another for America, to meet local compliance regulations.

  The data pipeline platform should support identity and access management to ensure that only authorized identities have access to specific jobs or steps within workflows. For instance, if your ETL process consists of several workflows, and one of them handles highly confidential data, the platform should allow you to restrict access to that workflow while keeping the others accessible. This capability helps you meet the security requirements without needing separate platforms for different data sensitivity levels. Ideally, the platform should provide built-in support for such isolation that enables efficient and secure data management.

Data processing pipelines can output the data to either a search index or a model training pipeline. Depending on the use case, see the sections on [search indexes](#considerations-for-a-search-index) or [feature stores](#considerations-for-a-feature-store).

## Considerations for a search index

The search index is designed to store contextual or grounding data to send to the model inference endpoint, along with the prompt. Both calls, the index query and the inference endpoint invocation, take place in the context of servicing the same client HTTP requests. Unlike ETL processes that handle offline and batch jobs, this index supports real-time inferencing, which requires high performance and reliability. It's specialized for AI queries and offers features like keyword indexing and filtering, which aren't typical of big data stores. The goal is to have a high-performing, _write-once, read-many_ data store that supports impromptu and fuzzy queries. This data store can provide relevant results without precise queries.

### Functional requirements

- **What types of search does the search index support?**

  Queries that the system receives are essentially searches, and the index needs to support rich search capabilities. For RAG, vector search is nonnegotiable because data is stored as calculated vectors, or embeddings, which are used for searching.
  
  Vector search is powerful, and combining it with filtering and full-text search enhances the search index's effectiveness. Your data design should account for combining these types of searches, such as vector, full-text search, filtering, and special data types like geo-location. 
  
  Your data design should explicitly state those requirements. For more information, see [Efficient querying in the data design](./grounding-data-design.md#efficient-querying).

- **Does the index support multimodal data?**

  Choose index technologies that support multimodal data. For instance, AI searches can analyze an email, convert an image within it to vectors, and store the description in the index. Use this function to search across various content modalities, including images, videos, and audio files.

- **Does the index support automatic update capabilities when the data in the data sources changes?**

  Choose an index that has automatic update features. If one isn't available, you need to manually detect and push changes to the index. With these capabilities, the indexer can detect changes in data sources and pull updates automatically. By offloading this responsibility to the platform, you can reduce operational overhead and simplify the maintenance process.

### Nonfunctional requirements

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

## Considerations for a feature store

  For discriminative models, your data design might include an intermediate data store that caches data for extra refinement. This store, known as a feature store, allows data scientists to store features as a final step, outside of the aggregated data store.

  The feature store helps catalog data for multiple uses by adding metadata like generation time and origin. This intermediate landing spot is ideal for _golden training data_.

  The managed feature store in Machine Learning is a data storage option that integrates with MLflow and other tools. It fetches and trains data from the aggregate data store, adding a reusable layer for better data lineage and formal identification within Machine Learning.

  When you use a feature store, treat it like a data store with security and access considerations. 

## Considerations for an offline inferencing data store

  In some scenarios, use of a separate store is appropriate for faster future lookups because inferencing is done on pre-collected and pre-calculated data, in advance. In this process, the user request never reaches the AI model. There are several benefits:
  
  - Improved efficiency and user experience by reducing latency. Results are served faster for frequent queries, such as generating FAQs as the result.
  - Inference calls can be scaled out more easily as a batch process without the constraints of real-time processing.
  - Allows prevalidation to ensure accuracy before production.
  - Because the request isn't directed to the interference endpoint, it reduces the load, contributing to the reliability of the workload.
  - Could be more cost-effective as it reduces the need for high-performance hardware required for real-time processing.

  However, this approach is only effective if you can predict the possible requests _and_ a significant portion of the predictions are expected to be requested by users. For scenarios with fewer repeated requests, an offline inference store might be less effective.
  
  The data store for this scenario should be optimized for read operations, must be able to handle large volumes of data and provide efficient retrieval. It should also be able to integrate into the aggregated data store. Any store with those capabilities can be considered, such as Azure Cosmos DB, or even a table storage.

## Resources

These articles provide more details about Azure products that we recommend as technology options for the considerations discussed in this article.

- [Machine Learning](/azure/well-architected/service-guides/azure-machine-learning)
- [Blob Storage](/azure/well-architected/service-guides/azure-blob-storage)
- [Azure Databricks](/azure/well-architected/service-guides/azure-databricks-security)
- [Data Factory](/azure/data-factory/)
- [AI Search](/azure/search/search-what-is-azure-search)
- [Azure Cosmos DB](/azure/well-architected/service-guides/cosmos-db)
- [Azure Cache for Redis](/azure/azure-cache-for-redis/cache-overview)

## Next steps

> [!div class="nextstepaction"]
> [Design area: MLOps and GenAIOps](./mlops-genaiops.md)
