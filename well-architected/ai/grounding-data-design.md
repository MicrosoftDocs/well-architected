---
title: Grounding Data Design for AI Workloads on Azure
description: Learn about data layout considerations and best practices for running AI workloads efficiently, which ensures optimal performance and scalability.
author: PageWriter-MSFT
ms.author: prwilk
ms.date: 05/28/2025
ms.topic: concept-article
ms.update-cycle: 180-days  
---

# Grounding Data Design for AI Workloads on Azure

For AI applications, the Well-Architected Framework approach to data design must address nonfunctional requirements like operability, cost, and security and adhere to the core principles of [Azure Well-Architected Framework pillars](/azure/well-architected/pillars). It should also consider functional requirements such as data ingestion, preparation, and validation.  

The AI model that you choose affects subsequent data design decisions. This article discusses key architectural considerations for foundation models that need augmentation to enhance result relevance. These models are typically generative.

Generative AI models are prebuilt or pretrained, which allows you to immediately use them without making modifications. However, out-of-the-box models often don't meet specific workload requirements. To address this issue, models are augmented with context-specific data to improve their performance. For example, you can use the GPT model in various use cases. These applications include retrieving information from documents, providing IT Helpdesk support, and summarizing complex information. To use foundation models to meet your specific needs, it's important to understand these considerations.

> [!IMPORTANT]
>
> Data design is an iterative process that's based on statistical experimentation. Generative AI applications send queries to the model that includes the prompt and context data. To refine data design, the prompt and context data should both be iterated. The iterative process should include preprocessing, selecting embeddings, and chunking. These steps help create data that's suitable for an index. For more information, see [Designing and developing a retrieval-augmented generation (RAG) solution](/azure/architecture/ai-ml/guide/rag/rag-solution-design-and-evaluation-guide).
>
> As you experiment and iterate, keep the consumption use cases in mind. Adjust the data design based on actual query patterns. Determine what's acceptable through refinement and testing.

In a solution, you can use a combination of generative AI and discriminative AI models to fulfill the workload requirements. For more information about training data, see [Training data design](./training-data-design.md).

## Recommendations

Here's the summary of recommendations provided in this article.

|Recommendation|Description|
|---|---|
|**Anticipate user queries**. |Understand the expected types of questions related to your source data and their expectations of freshness. This understanding helps you design your data pipelines and indexes to provide relevant grounding data.<br>|
|**Externalize data to a search index**. | Instead of querying directly from the source system, use a search index. Evaluate different index technologies based on workload requirements. Create a capability matrix to assess the best fit for your needs. Consider powerful search index technologies like Elasticsearch or AI Search.<br><br>&#9642; [Indexing](#indexing)|
|**Develop an ingestion strategy**. |Develop a comprehensive index management strategy that covers data ingestion and preprocessing. Remove noisy or irrelevant data by addressing inconsistencies and duplicates and standardizing to a common schema. Convert source formats and types to data types that facilitate querying and analysis. <br><br>&#9642; [Data preparation](#data-preparation)<br>&#9642; [Data volume rescoping](#data-volume-rescoping)|
|**Design your index for maximum relevancy**. |Enable features like filtering, sorting, and metadata handling on specific fields to improve query efficiency. For instance, label fields as searchable only if you intend to search on them. To avoid unnecessary storage costs, don't make every field retrievable without a specific use case. <br><br>&#9642; [Schema design](#schema-design)<br>&#9642; [Index capabilities](#index-capabilities)<br>&#9642; [Efficient querying](#efficient-querying)|
|**Update your index to prevent inferencing on stale data**. |When you update an index, consider adopting a side-by-side deployment strategy for maintenance. Rebuilding the index ensures that deletions and updates are handled because the index becomes a fresh data set. This approach allows thorough testing of the data before making the index live. When you make changes to indexes, coordinate schema modifications with code updates. This practice ensures seamless transitions.<br><br>&#9642; [Index maintenance](#index-maintenance)|

## Types of data

You can augment generative AI models by using context data during inference, or optimize them further through a fine-tuning process. Both approaches need supplementary data that provides the model with more context. The model uses that context to answer the user query and forms the answer according to the expectations. Typically, you use the following data types:

- **Source data** is existing data in production. This data can be structured, such as data in databases, or semi-structured, like JSON files. It can also be unstructured, like documents, images, and audio files.

- **Grounding data** is information provided to a language model at inference time to help it generate responses that are more accurate and relevant to a user's query. This process of *grounding the model* involves supplementing the model with data that was not part of its original training. The grounding data can come from internal systems, such as databases, or from external sources. It's combined with the user's query to construct the prompt that is sent to the model during a specific inference call.

  This data should be quickly searchable and retrievable by the application. You can store it many ways. You can use a separate index that's accessed in real time. Or, if your source data platform supports built-in indexes, you can run queries directly. 

- **Behavioral instructions** is information that's used to influence the model so that it can adapt to specific tasks, domains, or response styles for future inferencing requests. For example, if the model is expected to provide answers in a specific grammatical style, that style guide would serve as fine tuning data.

- **User data** includes information provided by users during interactions with the application. When you interact with generative models, stateful interactions occur. These models lack inherent memory and treat each interaction as atomic.

  When you manage stateful interactions, also known as *TURN data* in chat applications, it's important to store data for the shortest time necessary. Ideally, this data should be destroyed after the session ends. However, there might be operational or compliance reasons that require you to retain certain data, such as the original question or the model's response, beyond the session's duration. When possible, avoid storing this data past the session.


## Source data querying

Grounding data can be sourced from the source data platform if it supports advanced querying capabilties. For example, built-in vector indexes, that allows you to run semantic queries directly. Another option is graph databases, which makes it easier to model and query relationships between entities. For example, you can use a graph database to find products preferred by users aged 20–30 by linking user profiles, orders, and product data. This relationship-based querying is more intuitive and efficient in graph databases compared to traditional databases.

Querying the source database directly simplifies security measures, when compared to indexes where techniques like security trimming can be complex and error-prone. However, using the live system for data queries has performance tradeoffs as it can impact current users due to added load. To mitigate this, one option is to create a replica of the database for querying purposes. 

## Indexing

The core of data design includes efficiently storing and managing foundational data. This approach ensures that data can be augmented to achieve the highest level of relevance.

A simple AI strategy might involve querying the source data for each user interaction. However, this approach isn't practical because of the high costs and complexities of direct data source interactions. Instead, you should repurpose source data as a copy in an index that's optimized for search and retrieval. The goal of this approach is to improve the model's comprehension and its ability to generate relevant responses.

Consider a banking workload that stores details related to user bank accounts and preferences and financial transactions in a data store. In a generative AI scenario that uses a RAG pattern, grounding data is created and indexed with context so that the model can give relevant responses. For instance, by providing relevant data about user transactions for context during inferencing, the model can answer questions related to the user's spending patterns in the last quarter.

### Specialized index technology

Consider externalizing the grounding data to a search index. Use this approach instead of querying directly from the source system.

There are benefits of using the search index. You can model and transform the copy of the data according to the expected queries. Direct queries to the primary source are problematic because there's a possibility that source data isn't accessible. An index ensures that the data remains available as long you deem it relevant to the application. You also avoid stressing the source data system. This strategy ensures that AI-related queries don't affect its primary use case.

Some technology options have self-indexing capabilities. Indexes can reach out to data sources and incorporate their data. For this option, network considerations are key. If the index needs to connect to databases, there are potential issues, such as network latency and reliability.

There's an initial cost to import data. After the data is in your index, you won't need to move it again unless there are changes or updates. Data management over time is a crucial aspect of index design. For more information, see [Index maintenance](#index-maintenance).


#### Default or custom index

Certain technologies support automatically building a default index for your data. This index is generated on data ingestion with minimal input. The index has out-of-the-box capabilities. A default index might be acceptable for proof of concepts and some production scenarios.

Some scenarios might require you to have a custom index schema to improve relevancy based on specific workload requirements. Those requirements dictate how you design the schema, enable index capabilities, and include relevant metadata.

#### Schema design

You can think of indexes as structures that organize and optimize data for retrieval. More specifically, they organize data in documents and fields of a table. Consider the following points:

  - **Index topology**. Evaluate whether to colocate all data in a single index or distribute across multiple indexes. This decision significantly affects query performance, index maintenance, query simplicity, and dissimilar field configuration (or schema) between documents.

    For example, consider user queries that request content in a specific language. The simplest data design choice is possibly translating all languages into one language and storing it in a single index. Or, data can be stored in all languages in a single index. This choice results in multiple documents for each language. The index's filtering capability could be used to restrict the results to the desired language. Alternatively, each index could contain the translated versions for a given language as expected in the query.

    In some situations, you might need multiple search indexes. This approach allows you to independently optimize each index for maximum relevancy from your search queries. For example, an HR employee handbook and a product maintenance manual serve different purposes and audiences. By indexing them separately, you can tailor the schema and search queries for each, which improves user experience. This approach can be complex to implement and requires an orchestrator to facilitate calls to each index. The orchestration component is described in [Application design for AI workloads on Azure](application-design.md).

    > [!NOTE]
    > The choice between the two topologies and the data segmentation strategy depends on workload requirements, use cases, and user expectations.
    >
    > Doing cross-index queries can be challenging and can affect search relevancy. In worst case scenarios, there might be manual sifting through results, deciding which ones fit the criteria. This process introduces latency and adds complexity. In contrast, a single index approach is simpler and more straightforward. Relevancy can be improved by using index capabilities such as filtering.
    >
    > In some cases, compliance considerations lead to the need for separate indexes. For instance, if business requirements demand that data is isolated between Europe and America, multiple indexes might be inevitable.

  - **Document design**. Align your data design with expected user queries to optimize relevancy. Consider how each document should serve queries. For search indexes, prioritize relevant documents and refine the results to a concise set that's densely packed with relevant information.

  - **Field design**. Configure your index fields to support search performance and relevance. Your index fields should map to the document attributes you want to make searchable, retrievable, filterable, and sortable. They include embeddings, IDs, or any other data that can boost searching.

#### Index capabilities

Configure the search index fields to return the most relevant set of documents. The decision depends on the capabilities that the search index technology and workload requirements support.

- **Filter, search, and sort options**. Consider these options because they're directly related to use cases for augmentation. For example, _filterable_ determines true or false against a value provided in the query and returns relevant documents. For _searchability_, the attribute indicates whether the search query can reference the field. For instance, you might check if a text field contains specific text or if it's mathematically related to another vector. You can optionally assign a relative weight to that field as part of the search query. You can also make result sets _sortable_, which lists the results by relevance.

   One potential use case for filtering is performing **security trimming**.  Security trimming ensures that users only see the data they're authorized to access, even when using a shared index. For example, a single index might include a region field, and queries are filtered based on the user's region or role. The application accesses a shared index of documents but only returns results the user has permission to view. 

   > :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff.** Enabling capabilities to index fields increases space requirements, affecting costs. Only add capabilities that you intend to use.

- **Metadata**. Indexes typically have metadata associated with index fields. Metadata helps us understand and manage data by providing relevant details about it. When designing indexes, consider whether metadata are retrievable or only used for relevance determination. The decision affects compute costs because the underlying indexing process is different. Excessive metadata can unnecessarily increase the size of the index.
  
There are many technology choices for indexing. Many share similar characteristics, such as the ones listed previously. Some indexes might have extra features, like processing text and language analysis during indexing. To make text more suitable for indexing and searching, break text into tokens, convert it to lowercase, or remove stop words.

#### Efficient querying

Grounding data is used in generative AI applications to increase the accuracy and relevance of the responses to user queries. Consider user queries upfront. Understand what questions could be asked, who asks them, and how frequently they're asked. This information helps the application form context and understand what result might be relevant.

Typical types of searches are:

- **Vector queries** search for similar items based on their vector representations or data points in a high-dimensional space.
  
- **Keyword search** searches within the entire content of textual documents. It indexes and queries large volumes of text data and is commonly used in search engines, databases, and document management systems.
  
- **Semantic ranking** improves the relevance of search results by reordering them based on their semantic relevance to the query, promoting the most semantically relevant matches to the top of the list.
  
- **Hybrid search** combines different search types, like vector search, full text search, and semantic ranking, to further improve the relevancy of the search results.

To further improve model performance, combine search types.

The manner in which data is stored and processed effects query efficiency. Each time data is added to an index, compute cycles are needed for indexing. If indexing and responding to queries are done on the same compute resources, there could be contention. Ideally, an index should focus on the primary goal of answering queries efficiently and finding relevant documents rather than excessive indexing.

Cost and performance are key drivers of index design. Techniques like creating shadow copies can speed up querying. However, data duplication occurs through indexes, which incurs costs.

> :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff.** Index design should consider both cost and performance. Strike a balance by optimizing storage and prioritizing efficient query answering and relevant document retrieval over excessive indexing.

For technology choices for the data store, search indexes, such as Elasticsearch or AI Search, provide powerful search capabilities, including vectorized and relevancy-based searches. Alternatively, consider database options that support the type of data that you have and the types of queries you need because they're optimized for querying. Ultimately, it's about the capabilities offered by the options and the investment of building new skill sets on the team.

## Data preparation

Grounding data is based on existing data, which needs to be made suitable for semantic querying. Some queries to find relevant documents in the index can be literal matching. Other queries require fuzzy matching.

Before contextual data is ready to support inferencing requests to the model, there's an upfront preprocessing step that aims at cleaning, transforming, and structuring data. The goal is to reduce noise and bias, search efficiently, and maximize relevancy of the index searches. The choice tools or logic for preprocessing depends on the workload team, but there are some broad considerations.

### Data volume rescoping

Data volume rescoping involves adjusting the scope of data by expanding or narrowing it to create a tight index so that relevance is increased. Query efficiency is another significant concern. Storing unnecessary data negatively affects both of those goals. For example, consider location data for a user. If only the city portion is relevant, optimize by storing just the city text instead of the full text that represents the address.

Here are some broad considerations.

- **Data elimination**. Retain only what's essential for the product's functionality, discarding unnecessary details. Here are some common examples.

  - **Qualitative elimination**. One way to transition from a broad scope to a narrower more relative one is to eliminate low-quality data by selectively only choosing to index relevant source data. The challenge lies in programmatically identifying content that's not relevant for AI scenarios. While the content might be useful for other intents, such as auditing or completeness, including it in the AI workload risks reducing relevance. One way to flag such content is by using metadata that can be used at index population time if the content must be added to the index.
  
  - **Sensitive data**. Copying data from source data to an index might also bring over sensitive information. Respect the data classification labels applied at source and maintain the same level of sensitivity to this data set. When you deal with data that has personal information, don't store personal data unless you need it to respond to the query. For example, apply data classification when indexing emails. If an email is flagged as sensitive, avoid storing it in a general sensitivity data store.

  - **Normalizing and standardizing text**. Addressing typos and standardizing text is crucial for keyword-based indexes. A potential use case is translations, especially when dealing with multilingual content.
  
    This type of preprocessing is also needed for embeddings, which enables you to compare words based on their context and significance. However, one challenge occurs from the case sensitivity of words. Context matters, and there can be nuances, such as the semantic differences between the adjective "civic" and the proper noun "(Honda) Civic."

- **Data addition**. Augmenting context often relies on a process called _enriching_. This adds extra information, which makes it more meaningful. For instance, consider a text snippet. A human in the loop or AI creates relevant questions that can be answered using the context of the snippet. When you store these questions alongside the grounding data, user queries can be compared to the generated queries to evaluate document relevancy. Colocating this new data with grounding data can make chunked data more useful. Another example of enrichment is tagging content with metadata. This can uncover natural breaks in meaning, or semantic boundaries, which can be used to define chunks. These chunks may be different sizes, but each one is meaningful and can stand alone.

  Consider maintaining data lineage. It's important for AI workloads to track the source of data because that information can get lost when a system aggregates various components into one index. This information might not ever be exposed to users, but information about data origins is crucial for internal data governance teams. This metadata isn't necessarily for the model. It helps maintain transparency and accountability.

  > :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff.** On one hand, adding new data increases the chances of finding relevancy within the data set. However, this benefit comes at a cost. Specifically, the computational resources required to process and manage that  field. The time spent collecting and storing data can be substantial. Be aware that overloading with unnecessary fields can strain resources.

- **Processing text data**. Consider techniques such as synonyms, stemming, and semantic proximity to enhance relevancy. Delegate these techniques to tooling if possible. Some technologies, such as Elasticsearch or AI search, offer such features for preprocessing data during index creation.

#### Data type morphing

Index fields in a datastore are data-typed to serve a specific purpose. Numeric fields facilitate efficient querying, text fields allow for text-based searches, and Boolean fields handle binary information.

Source data typically exists in various types of data, like text, images, and tables, and processing that data might be complex. You might have to extract key-value pairs, identify section headings for semantic chunking, recognize specific identifiers, and so on.  

For example, if your source data contains images, they aren't inherently searchable. They need to be converted into vector representations to enable efficient semantic searches and comparisons. If relevancy is tied to the data behind these formats, invest in extraction of the data. Transform source data types to functional data types that help in querying and analysis.

#### Chunking and the context window size

A context window refers to the maximum amount of text that the model can handle at one time when generating a response. The context window size limits how much input (user query, grounding data, system instructions) the model can process in a single interaction.

Grounding data often contains a large volume of information. Models only support up to a certain amount of tokens in one request, beyond which text can be truncated or ignored. If the context window is large, the entire text can be fed directly to the model. This strategy called _full-document grounding_ can improve accuracy, because the model has access to the full content. However, there's a trade-off. Sending large documents consumes a lot of tokens, which can increase costs. 

Alternatively, if the context window is smaller, strategies like *chunking* can be efficient. It involves dividing text into smaller pieces that can be individually processed and indexed. This allows for efficient search and retrieval despite token limitations. Check the maximum number of tokens that your choice of model can handle. Your chunks shouldn't exceed that limit.

  > :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff.** Full-document grounding offers high accuracy and simpler implementation by allowing the model to process the entire context at once. However, it comes with high token costs, increased latency, and limited scalability due to context window constraints. In contrast, chunking is more complex to implement and maintain but is more flexible. It enables targeted responses and faster processing though it may be less accurate if the chunks aren't well designed.

There are many techniques for implementing chunking. For more information, see [Chunking approaches](/azure/architecture/ai-ml/guide/rag/rag-chunking-phase#chunking-approaches).

_Embeddings_ is also another design strategy that enables vector search capabilities. Embeddings is a mathematical representation of an object that's generated by AI models based on grounding data. They're stored in the index and add more context that helps complex queries yield results with better relevancy. For more information, see [Generate embeddings](/azure/architecture/ai-ml/guide/rag/rag-generating-embeddings).

## Index maintenance

Maintenance over time is a crucial aspect of index design. For static data, where documents remain unchanged, index maintenance is straightforward. But, most indexes are dynamic. Over time there might be new data added and the index schema might need new fields. Conversely, some data and fields might need to be deleted if they're no longer relevant. Commonly used technology options for indexers have features to handle updates automatically. For information about the recommended index characteristics, see [Considerations for a search index](data-platform.md).

### Maintenance criteria

- **Functionality updates**. The index might need to be updated if there's a change in application functionality. This situation happens when new questions are asked. To accommodate these changes, you might need to add new fields to the index or modify filtering, searching, or text-processing options on existing fields.

- **Data deletion**. Data deletion is challenging because you need to analyze available and missing data to determine what's irrelevant. To exclude outdated content from an index, consider the use of metadata that prevents search engines from indexing specific pages or content. Also, when you choose storage options, select a technology that supports deletions efficiently. For example, blob storage supports soft deletes. If you use AI search and loading documents from storage, blob storage can detect removed documents and delete corresponding entries. This approach isn't ideal but it's necessary when reindexing is costly because of a large index size.

  The concept of the _right to be forgotten_ refers to an individual's right to have their personal data removed from online platforms or databases. Ensure that you have policies in place to remove personal data if it was used for training. You can address this requirement by reindexing your data set. If data is deleted from the transactional database, subsequent index updates reflect those changes.

- **Maintaining compatibility**. Applications often require specific data structures, and any deviation can disrupt their functionality. For example, if a field is removed and the application requests that field, then a failure condition might occur. As you would for a traditional database, adopt a forward compatibility mindset for indexes and maintain a level of rigor. When you make changes to the index, like adding or removing fields, coordinate schema changes with code updates.

  > :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff.** Add, update, and delete actions against an index are expensive. Consider the frequency of updates and the cost to performance based on data store size and efficiency. Keeping obsolete documents in the index incurs storage, maintenance, and querying costs.

#### Deployment strategy

**Deployment strategy**. There are two main strategies for updating the index.

- **Side-by-side deployments**. In this approach, a new index that has updates lives alongside the existing one. After the new index is tested and fully operational, queries are switched to use the updated index. The application remains unaware of this switch because it only interacts with the new index. If you discover other issues after the new index is deployed to production use, you can revert to the old index. This approach minimizes downtime and ensures continuous availability.

  Side-by-side updates work well when the cost of rebuilding the index is reasonable and can be completed in a reasonable time frame. In general, strive to keep indexes as efficient as possible because larger indexes consume more resources. Regularly monitor and maintain indexes to avoid unnecessary growth.

> [!TIP]
> When you perform resource-intensive data pre-processing tasks like entity recognition, lookups, and calculations, consider saving a copy of the results. This approach ensures that when you need to rebuild the index, you can avoid redoing all the computations. Some calculations might no longer apply because of deletions or updates, but many will remain relevant.

- **In-place update deployments**. This approach directly modifies the existing index. Saving the cost of duplication can be beneficial, but it also introduces risks because of potential downtime and resource-intensive operations. If your index is large and rebuilding it from scratch exceeds your desired update frequency, you can consider using in-place updates. However, this approach is challenging and carries the risk of breaching your service-level objective (SLO).

  > :::image type="icon" source="../_images/trade-off.svg"::: **Tradeoff.** Evaluate the cost of doing side-by-side deployments of indexes against doing in-place updates that deploy additions, updates, and deletions. In most cases, you should use side-by-side updates instead of in-place updates. When an index is rebuilt, the process effectively handles deletions and updates because it creates a completely new data set. This strategy provides the opportunity to test data. Even though side-by-side deployments temporarily duplicate data and incur additional costs, the benefits in testing and performance evaluation often justify this storage requirement. Before you make an index live, examine the data to ensure that it aligns with your expectations.

- **Scheduled updates**. Instead of maintaining continuous real-time communication with data sources, you can refresh grounding data periodically. This approach ensures that the data stays relevant through scheduled updates, which eliminates the need for constant interaction.

- **Emergency updates**. Unexpected situations can occur, such as unwanted data inadvertently leaking into the search index. If this issue occurs, you might need to take immediate action, such as removing specific documents or adjusting data within the index. Regardless of the deployment strategy you choose, such as side-by-side updates or in-place updates, always plan for the possibility of emergency operations.

- **Self-updating index**. If your indexing technology supports automatically updating the index to keep it synchronized with an external data source, it might be able to automatically process changes in the data. Data changes include additions or deletions, without manual intervention. Keep in mind that every change triggers an operation in the index, which consumes resources. The index might remain responsive to queries, but its capacity for handling them could be reduced during the update process.

#### Freshness operations

Measure the window of time between source data creation or modification and its addition to the index as an indicator, and track it against SLOs. This indicator drives data decisions around updating your data pipeline design to ensure that data is available in your index when you need it. An index should only be as fresh as is required.

To maintain freshness, you can either rebuild the index entirely, or incrementally update it to stay synchronized with the original data sources. Both methods ensure that the index remains current and accurate.

Upfront investment in fine tuning the model might be less expensive than implementing a RAG pattern, prompt engineering, and data augmentation methods.

## Next steps

> [!div class="nextstepaction"]
> [Design area: Data platform](./data-platform.md)
