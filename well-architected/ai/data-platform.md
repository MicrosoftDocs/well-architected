---
title: Data platform for AI workloads on Azure
description: Data sources on Azure for running AI workloads.
author: PageWriter-MSFT
ms.author: prwilk
ms.date: 07/22/2024
ms.topic: conceptual
---

# Data platform for AI workloads on Azure

A data platform is an integrated set of technologies designed to manage workload requirements by ingesting source data, then filtering, aggregating, and preparing it for consumption. 

Data has distinct characteristics based on its intended use. We highly recommend understanding the principles of good data pipeline design before exploring the technological capabilities described in this article. For more information, see [Training data design](./training-data-design.md) and [Grounding data design](./grounding-data-design.md). 

The platform also fulfills storage needs as data rests at certain points in the pipeline. If the workload is complex and handles large scale data, you can distribute pipeline tasks among various components. For simpler use cases, evaluate whether you can use the source data in a store that offers those combined capabilities.

Ask yourself the following questions, before designing overly an complex architecture for your data platform. It's always best to keep things simple when you can. 

- Can your application have the expected predictive power by ingesting data from a single source?
- Does your initial choice of a store support data warehousing capabilities?
- Is the source data already optimized for AI searches?

If yes, you can simplify your architecture by allowing the application to access the data source directly. This eliminates the need for big data architecture components like data ingestion, analytical store integration, and external data processing. If the source database can handle the required searches, integrating the search index capability directly into the source database can be a practical approach. Of course, keeping in mind that the source can cost-effectively scale to meet new demands. 

For instance, Cosmos DB supports vector search, so you might not need an additional index. Another use case is using read replicas as endpoints for search operations. For SQL databases with read replicas, direct searches to these replicas can optimize performance. Take advantage of the database's built-in capabilities to simplify the architecture, as much as possible.

But, for large scale workloads, it's not that simple. 

Ingesting data from multiple data sources and orchestrating searches across various platforms can become complex and inefficient. Also, you would still need some extract, transform, and load (ETL, ELT, EL) processes to reshape the data within the data store. The more processing required, the more complex the scenario becomes, with numerous components added to the architecture to handle the end-to-end pipeline from ingestion to serving queries. Many big data technologies are highly specialized and are built to handle those processing tasks effectively.

One such technology is the search index. The primary advantage of adding a separate index is its ability to efficiently manage queries and process large volumes of data with high throughput. This offloads AI capabilities from the original data source, enabling the index to focus on its main function of serving queries.

Choose a platform based on its specific functionality and purpose, considering both functional and technical requirements. If your architecture is evolving to handle complex use cases, focus on the sections on aggregated data store, processing pipelinese, and search index. 

## Considerations for storing aggregated data

In AI workloads, data moves through various stages of storage and processing with the help of pipelines that orchestrate workflow in between. One key stage is a data store that contains data ingested and aggregated from multiple sources. This store is needed to conduct processing until it reaches a state suitable for training or indexing. The primary focus is on ensuring the data accurately reflects its source.

> [!NOTE] 
> An alternative approach is to directly access data sources. However, this can lead to performance issues, as it may overload the source systems with AI features. There could also be data access problems. To avoid these issues, it's recommended that data is copied to this store.

The data platform for this store should meet security standards applied at data sources, be cost-effective, and support integration with ETL/ELT/EL processing tasks. Options vary based on data volume, from basic storage to big data technologies. Choose economical storage that helps you acheive just enough reliability and performance.

This section provides guidance on the capabilities to consider when selecting a data store technology. Details on data processing are also covered in [Data processing pipelines](#considerations-for-processing-data).

#### Functional requirements

- **Can the platform handle various data formats?** 

  The data store should not only have the capability to store various data formats but transform them to other formats, if necessary. 
  
  Suppose your ingestion pipeline sources data from a relational database and Parquet format (supports both structured and semi-structured data). You want to convert relational data to Parquet, as per its schema definitions. The data platform should have built-in capabilities to do that transformation without having you to write custom code. 

- **Do you expect to store multiple versions of the data?**

  Data values and schemas can change over time and managing multiple versions of the data becomes important. 

  Source systems typically store only current data, not historical data. If it's important to retain historical data, you might need to duplicate large data sets from source systems. In this case, versioning can disambiguate current data from historical data. 

  In some cases, you might need to maintain copies of data for different use cases. To support this, forking data might be necessary. Each fork can independently mutate to enhance its quality and usability. Your data platform should be capable of maintaining proper versioning of those forks. 

  Your data platform should be capable of storing versions of data over time, providing historical context. This is beneficial for processing and training AI models, as it offers multiple observations rather than just a single point in time.

- **Does the platform have built-in data lifecycle management capabilities?**

  Data Lifecycle Management (DLM) is a process for managing data from its creation to its deletion, with stages such as data collection, storage, usage, archiving, and disposal.

  Without DLM, data can grow uncontrollably, often resulting in multiple copies as it moves through quality tiers. The data platform should have lifecycle management capabilities to prevent unbounded data growth.

  Consider this scenario. The preprocessing step needs to be repeated to refine the data until it reaches an acceptable quality for training purposes. Your data platform should be able to delete intermediate copies of the data.

  In some cases, you might need to retain data for regulatory audits. The data platform should have cold storage capabilities for infrequently accessed data, allowing you to archive it at a lower cost.

- **Does the platform support data governance features?**

  For AI workloads, audatability is an important aspect. The data store should maintain audit trails that can track data access, ensure privacy, and understand data origins.

  A data dictionary feature allows for the management of metadata, data types, purposes, and lineage. This is especially important when data is ingested from multiple sources.

- **Do you plan to conduct train with production data?** 

  There are two approaches model deployment and code deployment. In model deployment, production data is used in development, which requires stringent security measures. In code deployment, the model doesn't see production data until it's in production. This simplifies security concerns in development environment, however can add to compute costs. Whichever approach you choose, your data platform should support separate environments for development and production. 
  

- **Are you prioritizing convenience features over key functional features?**

  When choosing a data platform for AI/ML, don't rely just on its notebook capabilities. While notebooks are useful for exploratory data analysis (EDA), they shouldn't be the main deciding factor. Compute resources for notebooks are typically outside the scope of the aggregation data store. They are usually integrated with resources such as in Azure Machine Learning (AML).


#### Non-functional requirements

- **How much data do you expect to store?**

  AI workloads generate a lot of data. With multiple versions and added metadata, volume can increase significantly. 
  
  Scalability is important, involving both storage and throughput. The data platform must efficiently consume data from the ingestion pipeline, handling data volume, managing concurrent writes, and ensuring individual write performance without degradation. Those criteria also applies to the processing pipeline that reads, processes, and perhaps even writes back to the store.
  
  When making a decision, consider the entire process because ingestion and processing often occur simultaneously. This requires a design that can manage frequent data movement and processing. The data platform should offer high levels of parallelism to process data effectively.

  The platform technology should emit telemetry that gives meaningful insight into the throughput and performance of the read and write operations. 

- **Is this data store a critical component that contributes to the reliability target of the workload?**

  Choose a data store that enhances both reliability and scalability by using multiple instances. Big data stores often have a built-in controller that orchestates processing of data across instances. If one copy fails, another can be used. 

  Keep in mind that data won't serve its purpose if isn't correct or accessible. The data platform should provide guarantee on durability making sure the data remains intact. Also, the APIs that query the data are accessible. Additionally, consider data stores that have backup features.

  In general, you don't need to back up this data. However, if the cost of aggregating data each time from scratch is significatly high, you can consider rehydrating the data from a backup.

- **Do you have any cost constraints?**

  If data reliability and performance are sufficient, consider the cost impact. 

  The system should be optimized for _write once, read many_ to avoid overspending on data storage. The training or grounding data is important but not critical like a production database, which requires instant responsiveness. The focus is on balancing cost and with just enough efficiency to maximize return on investment.

The preceding requirements might naturally lead you to consider using a data lake, as it offers data lifecycle management, quality tiers, observability, and support for diverse file formats. If your workload already uses a data lake, take advantage of that resource to meet your AI needs. Alternatively, you can choose other storage options, such as Azure Blob Storage, which provides some level of data lifecycle management, monitoring capabilities, and high transaction rates.



## Considerations for processing data

Data in the aggregate data store must be processed to increase it's utility downstream. This task is done by ETL pipelines and is most pertinent at points:

- **Ingestion layer**

  The pipeline is responsible for collecting data from various sources and moving it to the aggregate data store. During this process, the pipeline typically performs basic preprocessing and could even structure the data in a queryable format. 
  
  To minimize the need for custom code, it's advisable to offload much of this responsibility to a data platform. When selecting a technology, consider the ETL characteristics required to support model training and augmentation.

- **Processing layer**

  Data from the aggregate data store undergoes extensive processing before it can be used for indexing or model training use cases. The pipeline requires similar expectations on reliability and scaling, as the ingestion pipeline. The main difference is the type of processing done on the data. 
  
  The process involves significant rescoping and restructuring of data. This includes tasks like entity recognition, integrating additional data into the dataset, and performing lookups. This process may include deleting unnecessary data and applying data logic through a data orchestration platform. 

The data processing stage can produce various outputs, which land in  different destinations for different intents. Its main goal is to prepare and transfer data from the aggregated data store for consumption by the final destination. The consumer can either pull data when needed, or the this processing layer can push data, when ready.

> [!NOTE] 
> In the context of machine learning and generative AI, it's important to distinguish between ETL, ELT, and EL processes. Traditional ETL is crucial for data warehousing and ORM mappings, where data must be transformed before loading into the target system due to schema restrictions. ELT involves extracting data, loading it into a data lake, and then transforming it using tools like Python or PySpark. In generative AI, particularly for retrieval-augmented generation (RAG), the process often involves extracting and loading (EL) documents to storage first, followed by transformations such as chunking or image extraction.

This section provides guidance on the capabilities to consider when selecting a data processing technology with ETL processes. 
  

#### Functional requirements

- **What's the support for connecting to data source(s)?** 

  Data to be processed might be stored in relational databases, big data sources, or various storage solutions. 

  Most data processing technologies support pre-built integrations that allow you to connect to various data sources without writing code. The connectors have features, such as copying data from source to sink, perform lookups, some level of data governance, and so on. There are tools that offer drag-and-drop features to avoid unnecessary coding.

  Choose a data platform that makes it easy to integrate with the expected data sources. 

- **Can the platform process various data formats?** 

  Data might come in various formats, structured data (database, JSONs), unstructured data (images, documents), or streaming data (from IoT devices). The pipelines should be able to handle the expected file types.

- **Does the platform offer features for data preparation and rescoping**? 

  Data intended for training or augmentation must be processed to become suitable for training, fine-tuning, or indexing. Your data design strategies should explicitly outline the requirements. 

  Certain considerations are described in: 

  - [Data preprocessing for training data](./training-data-design.md#data-preprocessing)
  - [Data preparion for grounding data](./grounding-data-design.md#data-preparation)
  
  As part of basic cleansing, duplicates are removed, missing values are filled, and extraneous noise is eliminated during ingestion. For certain use cases, such as implementing a Retrieval-Augmented Generation (RAG) pattern, lowercasing chunks is highly recommended.

  While these preprocessing steps are necessary, the platform must support rich data manipulation, tailored to your specific needs. This process involves loading data, rescoping, and transforming data. For certain models, the platform must be able to query external sources for document analysis, such as document intelligence or other AI tools. This work is needed for preparing the data and enrichment.

  If your data store supports this level of processing, you can localize this stage in the store  without moving it elsewhere. Otherwise, you'll need an external technology such as Azure Databricks or Azure Data Factory (ADF). These technologies are suitable for moving data and performing manipulations, such as filtering, filling missing values, and standardizing string casing. For more complex tasks, a job hosting platform is typically required. Spark pools can be used for big data orchestration.

  In certain use cases, you might want to externalize this responsibility to the consumer of the data. For example, AI models using Azure Machine Learning (AML) offer job processing capabilities to read, manipulate, and write data by using custom Python code.

  Another example is RAG implementation. A common processing step is chunking, where a document is divided into multiple chunks, each becoming a row in the index. Embeddings for these chunks, often generated by an OpenAI service, are also stored. In AI search, this process is orchestrated within the indexing workflow, whether using OpenAI or Azure AI Search.
  
- **Is there a built-in orchestrator for managing workflows**?
  
  The processing tasks are modular and run as jobs. The platform should have orchestration capabilities that breaks down the workflow into steps or jobs. Each job should be independently defined, executed, and monitored. 

  In complex workflows certain steps depend on the successful completion of previous ones. The orchestrator should handle job dependencies, making sure that tasks are executed in the correct order.

  Given that data design is an iterative process, the orchestrator tool should be flexible enough to modify workflows easily. You should be able to inject new steps or adjust existing ones without rewriting large portions of code.

  Azure Data Factory is a popular choice as it provides a rich feature set for managing data workflows. Azure DataBricks also has capabilities to manage complex workflows, schedule and monitor jobs. Also, consider the cost implications. For example, Databricks features might be extensive, but also costly. Open-source alternative, such as Apache NiFi, might be a more cost-effective.

  Ultimately, the choice of tool depends on what your organization allows and skills the workload team is comfortable with. 

#### Non-functional requirements 

When choosing a processing pipeline, it's crucial to balance throughput and observability. The pipeline must reliably process and land the necessary data for models or indexes within a sufficient timeframe. It should be lightweight enough to support current needs while being scalable for future growth. Teams must decide how much future-proofing is needed from platform to avoid technical debt later. Key considerations include the frequency and volume of data ingestion, the reliability of the process, and the need for observability to monitor and address issues promptly.

- **How much data do you expect to ingest?**

  For the ingestion and processing stages, consider the platform's scalability and speed for handling tasks. For example, you're expecting to load 10 terabytes of data daily into an index or for training a model. Your data ingestion platform should be able to process that much volume and with the expected throughput. In this case, using Azure Logic Apps may not be feasible, as it could fail under such a load. Instead, Azure Data Factory is better suited for this scale of data processing. 

  One way to handle high volume is through parallelism, as it allows for more efficient data handling and processing. Platforms such as Databricks can orchestrate tasks by creating multiple instances for the same job, distributing the load efficiently.

  Also, consider the tolerable latency and the complexity of the jobs. For instance, data cleansing involves validating and potentially replacing invalid fields, or masking sensitive information. These tasks, though basic, require significant resources because each row is processed individually, adding to the overall time.

- **What monitoring capabilities do you need?**

  Data processing pipelines should have monitoring capabilites and provide insights into the pipeline's performance and status of jobs.

  You must be able to track the progress of the jobs. Suppose the pipeline was scheduled to run data cleansing job that didn't complete or completed partially. There might be downstream impact on quality of data with which the model is trained and consequently might impact the predictive power. 

  Similar to other components in the workload, enable logs, metrics, and alerts on the data pipeline that gives insights into its behavior. Collect and analyze performance metrics to understand the efficiency and reliability aspects.

  Identify any gaps in the built-in telemetry and determine what additional monitoring you need to implement. This could involve adding custom logging or metrics to capture specific details about the job steps.
  
- **How much reliability do you expect of the data processing platform?**

  The reliability of a data processing pipeline varies based on the choice of platform. Azure Logic Apps, even though has orchestration capabilities might not be as reliable as Azure Data Factory. Data Factory hosted on an AKS cluster might have different reliability characteristics. 
  
  Single-instance setups are generally considered points of failure. Choose a platform that supports reliability features, such as multiple instances, to meet your requirements. 

  The platform should also support resiliency features. For example, the orchestrator should should automatically retry failed tasks, reducing the need for manual restarts.

  Batch processing can be less reliable than inferencing, depending on data freshness and latency requirements. If training occurs weekly and processing takes one day, occasional failures are acceptable because there's enough time to retry. 

- **Are there any cost constraints?**

  When considering the cost-effectiveness of a data processing pipeline, it's important to choose a solution that meets your needs without unnecessary expenses. If your requirements don't justify the advanced features of Azure Databricks, a more economical option like Data Factory might be sufficient. Additionally, open-source tools such as Apache Airflow or Apache NiFi can provide robust capabilities at a lower cost. The key is to avoid overspending on features you don't need and select a platform that balances functionality and cost efficiency.


- **What are the security requirements on the data to be processed and the workflows?**

  Be clear about the security, privacy, and data residency requirements. For example, are there any geographic regulatory requirements? Comply with data residency requirements by ensuring  data is stored and processed within specific regions. You might need to run separate pipelines for different regions, such as one for Europe and another for America, to meet local compliance regulations.

  The data pipeline platform should support identity and access management (IAM) to ensure that only authorized identities have access to specific jobs or steps within workflows. For instance, if your ETL process consists of several workflows, and one of them handles highly confidential data, the platform should allow you to restrict access to that workflow while keeping the others generally accessible. This capability will help meet the security requirements without needing separate platforms for different data sensitivity levels. Ideally, the platform should provide built-in support for such isolation, enabling efficient and secure data management.

Data processing pipelines can output to the data to either a search index or a model training pipeline. Depending on the use case, see the sections on [search index](#considerations-for-a-search-index) or [feature stores](#considerations-for-a-feature-store).


## Considerations for a search index

The search index is designed to store contextual or grounding data to send to the model inference endpoint, along with the prompt. Both calls, the index query and the inference endpoint invocation take place in the context of servicing the same client HTTP requests. Unlike ETL processes that handle offline and batch jobs, this index supports real-time inferencing, requiring high performance and reliability. It's specialized for AI queries, offering features like keyword indexing and filtering, which are not typical of big data stores. The goal is to have a performant, _write-once read-many_ data store that supports ad hoc and fuzzy queries, providing relevant results without needing precise queries.


#### Functional requirements

- **What types of search are supported by the search index?**

  Queries sent to the system are essentially searches and the index needs to support rich search capabilities. In the case of RAG, vector search is a non-negotiable because data is stored as calculated vectors, or embeddings, which are used for searching. 
  
  While vector search is powerful, combining it with filtering and full-text search enhances the search index's effectiveness. Your data design should account for combining these types of searches, such as vector, full text search, filtering, special data types like geo-location. 
  
  Your data design should explicit state those requirements. For more information, see [Efficient querying in the Data design](./grounding-data-design.md#efficient-querying).

- **Does the index support multi-modal data?**

  Prefer index technologies that support multi-modal data. For instance, AI search can analyze an email, convert the image within it to vectors, and store the description in the index. This allows for searches across various content modalities, including images, videos, and audio files.

- **Does the index support auto-update capabilities when the data in the data sources changes?**

  Choose an index with auto-update features. If not available, you'll need to manually detect and push changes to the index. With these capabilities, the indexer can detect changes in data sources and pull updates automatically. By offloading this responsibility to the platform, you can reduce operational overhead and simplify the maintenance process.  

#### Non-functional requirements

- **Is the index performant with large volume of data?**

  The index should be able to handle large amounts of data, be scalable, and perform well for heavy search workloads. The index not only stores the raw data but also all the metadata, enrichments, and entities associated with it. In the context of the RAG pattern, a single document that's split into multiple chunks, can result in a significant increase in data volume. 

- **Does the index have built-in reliability features?**

  Consider the alignment between the reliability of the inferencing endpoint (or the model) and the data store because they are dependent on each other. 
  
  The search process involves two steps: querying the data store and then querying the inferencing endpoint. Both steps need to have similar reliability characteristics. Balance your reliability objectives across both components to ensure effectiveness of the search.

  To ensure resiliency, the workload should support the expected number of concurrent users and have sufficient bandwidth to handle traffic surges. Ideally, the platform should survive zonal outages, adding an extra layer of resilience. 
  
  The data platform should be designed to prevent the use of corrupted index for inferencing. In such cases, you should be able to rebuild the index easily. The index should also support reliable swapping between indexes, using features like aliasing to minimize downtime during index swaps. Without such functionality, you might need to rely on a backup of the index. But managing a backup comes with additional complexity.

  From a workload perspective, understand the potential failure modes or stress indicators, such as throttling. For instance, while the system might normally support 50 concurrent users, during a reindexing process running as a background job, it might only support 30. In that case, timing of the background job becomes important. When you evaluate throughput of an index, include both frontend queries and backend jobs.

- **What are the main cost drivers of this technology?**

  When cost modeling, estimate expense associated with volume of data, number of queries, and throughput expected of the index. Keep in mind, indexes are mostly PaaS, where pricing is abstracted. Research tiers and their capabilities avoid overpaying for unused capacity or features.
  
  For example, Azure AI Search bills as units, which can include capacity, throughput, storage. Additional features can lead to extra charges. For instance, extensive use of image extraction features can result in a high bill. Dependencies, such as the skill set feature, which are outside the scope of the index but are part of data processing can incur additional costs. 

  Paying for a tier without using the full capacity can lead to overpaying. Similarly, the number of tables in your index and the ability to handle concurrent traffic affect costs.

  To understand the costs associated with Azure AI Search, see [Plan and manage costs of an Azure AI Search service](/azure/search/search-sku-manage-costs).


- **Does the security features of the index fulfill your security data design?**

  Your data design should clearly specify the security and privacy requirements. In development and test environments, where real production data is used, the index should support capabilities that comply with all access controls and traceability measures. Review security features such as data masking and the removal of personal information in the index.
  
  Prefer an index that has the ability to uniquely identify clients, through Microsoft Entra ID. Additionally, the search index should support document-level access controls to allow querying relevancy by identities. If the index doesn't offer those features, adjust your design to achieve similar capabilities, with query filters. For more information, see [Security filters for trimming results in Azure AI Search](/azure/search/search-security-trimming-for-azure-search).

  Ideally, the search index should align with network security requirements. For example, if we need to filter egress traffic to third-party sites and maintain observability, the index should offer egress controls. It should also support network segmentation. If the backend compute is in a virtual network, private connectivity for key components, including the index, is essential to avoid exposure to the public internet. The index should easily integrate with private networks and support managed identities for authentication via Microsoft Entra ID.


## Considerations for a feature store

  For discriminative models, your data design might include an intermediate data store that caches data for additional refinement. This store, known as a feature store, allows data scientists to store features as a final step out of the aggregated data store.

  The feature store helps catalog data for multiple uses, adding metadata such as generation time and origin. This intermediate landing spot is ideal for _golden training data_.

  The managed feature store in Azure Machine Learning (AML) is a data storage option integrated with MLflow and other tools. It fetches and trains data from the aggregate data store, adding a reusable layer for better data lineage and formal identification within AML.

  When using a feature store, treat it like a data store with security and access considerations. 

## Considerations for an offline inferencing data store

  In some scenarios, you may be able to invoke the model inference on pre-collected data in advance, instead of real-time. Offline inferencing implements this pattern by precalculating predictions for possible inputs and storing them in a separate data store. There are several benefits:
  
  - Could be more cost-effective as it reduces the need for high-performance hardware required for real-time processing.
  - Reduced latency and better user experience since offline inferencing eliminates the latency added by the model inferencing request.
  - More efficient and performant as the inference calls can be scaled out more easily as a batch process without the constrains of real-time processing.
  - Allows prevalidation to ensure accuracy before production.
  - Reduces the load on the inference endpoint, contributing to the reliability of the workload.

  However, offline inferencing is only effective if you can predict possible requests and a significant portion of the predictions will be requested at runtime. For scenarios with fewer repeated requests, pre-calcutaing may be less effective.
  
  The data store for this scenario should be optimized for read operations, can handle large volumes of data and provide efficient retrieval, such as Azure Cosmos DB, and Azure Cache for Redis. It should also be capable of integrating into the aggregated data store.

## Resources
These articles offer additional details on Azure products recommended as technology options for the considerations discussed in this article.

- [Azure Machine Learning (AML)](/azure/well-architected/service-guides/azure-machine-learning)
- [Azure Blob Storage](/azure/well-architected/service-guides/azure-blob-storage)
- [Azure Databricks](/azure/well-architected/service-guides/azure-databricks-security)
- [Azure Data Factory (ADF)](/azure/data-factory/)
- [Azure AI Search](/azure/search/search-what-is-azure-search)
- [Azure Cosmos DB](/azure/well-architected/service-guides/cosmos-db)
- [Azure Cache for Redis](/azure/well-architected/service-guides/azure-cache-redis/reliability)
