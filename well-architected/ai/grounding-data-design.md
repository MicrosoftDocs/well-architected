---
title: Grounding data design for AI workloads on Azure
description: Learn about data layout considerations for running AI workloads.
author: PageWriter-MSFT
ms.author: prwilk
ms.date: 10/14/2024
ms.topic: conceptual
---

# Grounding data design for AI workloads on Azure

For AI applications, the Well-Architected approach to data design must address nonfunctional requirements like operability, cost, security, and adhere to the core principles of [Azure Well-Architected Framework pillars](/azure/well-architected/pillars). It should also consider functional requirements such as data ingestion, preparation, and validation.  

The AI model that you choose affects subsequent data design decisions. This article discusses key architectural considerations for foundation models that need augmentation to enhance result relevance. These models are typically generative.

Generative AI models are prebuilt or pretrained, which allows you to immediately use them without making modifications. However, out-of-the-box models often don't usually meet specific workload requirements. To address this, models are augmented with context-specific data to improve their performance. For example, you can use the GPT model in various chatbot applications, such as retrieving information from documents, providing IT Helpdesk support, and summarizing complex information. Understanding these considerations helps you use foundation models to meet your specific needs.

> [!IMPORTANT]
>
> Data design is an iterative process that's based on statistical experimentation. Generative AI applications send queries to the model that includes the prompt and context data. To refine data design, the prompt and context data should both be iterated. The iterative process should include preprocessing, selecting embeddings, and chunking. These steps help create data that's suitable for an index. For more information, see [Designing and developing a retrieval-augmented generation (RAG) solution](/azure/architecture/ai-ml/guide/rag/rag-solution-design-and-evaluation-guide).
>
> As you experiment and iterate, keep the consumption use cases in mind. Adjust the data design based on actual query patterns. Determine what's acceptable through refinement and testing.

In a solution, you can use a combination of generative AI and discriminative AI models to fulfill the workload requirements. For more information about training data, see [Training data design](./training-data-design.md).

## Recommendations

|Recommendation|Description|
|---|---|
|Anticipate user queries. |Understand the expected types of questions related to your source data and their expectations of freshness. This understanding helps you design your data pipelines and indexes to provide relevant grounding data.<br>|
|Externalize data to a search index. | Instead of querying directly from the source system, use a search index. Evaluate different index technologies based on workload requirements. Create a capability matrix to assess the best fit for your needs. Consider powerful search index technologies like Elasticsearch or AI Search.<br><br>&#9642; [Indexing](#indexing)|
|Develop an ingestion strategy. |Develop a comprehensive index management strategy that covers data ingestion and preprocessing. Remove noisy or irrelevant data by addressing inconsistencies, duplicates, and standardizing to a common schema. Convert source formats and types to data types that facilitate querying and analysis. <br><br>&#9642; [Data preparation](#data-preparation)<br>&#9642; [Data volume rescoping](#data-volume-rescoping)|
|Design your index for maximum relevancy. |Enable features like filtering, sorting, and metadata handling on specific fields to improve query efficiency. For instance, label fields as searchable only if you intend to search on them. Prevent unnecessary storage costs by not making every field retrievable without a specific use case. <br><br>&#9642; [Schema design](#schema-design)<br>&#9642; [Index capabilities](#index-capabilities)<br>&#9642; [Efficient querying](#efficient-querying)|
|Update your index to prevent inferencing on stale data. |When you update an index, consider adopting a side-by-side deployment strategy for maintenance. Rebuilding the index ensures that deletions and updates are handled because the index becomes a fresh dataset. This approach allows thorough testing of the data before making the index live. When you make changes to indexes, coordinate schema modifications with code updates. This practice ensures seamless transitions.<br><br>&#9642; [Index maintenance](#index-maintenance)|

## Types of data

You can augment generative AI models by using context data during inference, or optimize them further through a fine-tuning process. Both approaches need supplementary data that provides the model with more context. The model uses that context to answer the user query and form the answer according to the expectations. Typically, you use the following data types:

- _Source data_ is existing data in production. This data can be structured, such as in databases, or semi-structured, like JSON files. It can also be unstructured, including documents, images, and audio files.

- _Grounding data_ comes from source data that contains information about topics not covered in the model's initial training data. Grounding data is combined with the user query to form the prompt that's sent to the large language model in the context of a specific inference call. Other data that you can include in the inference call are the system prompt, one-show or few-shot examples, and contextual data like previous interactions.

  This data should be easily searchable and quickly retrievable. Because of this, we recommend that you store the data in an index that's optimized for search. This index is accessed in real time while the user waits for the answer. Without this data, the model might produce incorrect results or not be applicable to what the user is specifically seeking.

- _Fine tuning data_ is information that's used to influence the model so that it can adapt to specific tasks, domains, or response styles for future inferencing requests. For example, if the model is expected to provide answers in a specific grammatical style, that style guide would serve as fine tuning data.

- _User data_ includes information provided by users during interactions with the application. When you interact with generative models, stateful interactions occur. These models lack inherent memory and treat each interaction as atomic.

  When you manage stateful interactions, also known as *TURN data* in chat applications, it's important to only store data for the shortest time necessary. Ideally, this data should be destroyed after the session ends. However, there might be operational or compliance reasons that require you to retain certain data, such as the original question or the model's response, beyond the session's duration. When possible, avoid storing this data past the session.

## Indexing

The core of data design includes efficiently storing and managing foundational data. This approach ensures that data can be augmented to achieve the highest level of relevance.

A simple AI strategy might involve querying the source data for each user interaction. However, this approach isn't practical because of the high costs and complexities of direct data source interactions. Instead, you should repurpose source data as a copy in an index that's optimized for search and retrieval. This approach aims to improve the model's comprehension and its ability to generate relevant responses.

Consider a banking workload that stores details related to user bank accounts and preferences, financial transactions, in a data store. In a generative AI scenario that uses a RAG pattern, grounding data is created and indexed with context so that the model can give relevant responses. For instance, by providing relevant data about user transactions for context during inferencing, the model can answer questions related to the user's spending patterns in the last quarter.

### Specialized index technology

Consider externalizing the grounding data to a search index. Use this approach instead of querying directly from the source system.

There are benefits of using the search index. You can model and transform the copy of the data according to the expected queries. Direct queries to the primary source are problematic because there's a possibility that source data isn't accessible. An index ensures that the data remains available as long you deem it relevant to the application. You also avoid stressing the source data system. This strategy ensures that AI-related queries don't affect its primary use case.

Some technology options have self-indexing capabilities. Indexes can reach out to data sources and incorporate their data. For this option, network considerations are key. If the index needs to connect to databases, there are potential issues, such as network latency and reliability.

There's an initial cost to import data. After the data is in your index, you won't need to move it again unless there are changes or updates. Data management over time is a critical aspect of index design. For more information, see [Index maintenance](#index-maintenance).

#### Default or custom index

Certain technologies support automatically building a default index for your data. This index is generated on data ingestion with minimal input. The index has out-of-the-box capabilities. A default index might be acceptable for proof of concepts and some production scenarios.

Some scenarios might require you to have a custom index schema to improve relevancy based on specific workload requirements. Those requirements dictate how you design the schema, enable index capabilities, and include relevant metadata.

#### Schema design

You can think of indexes as structures that organize and optimize data for retrieval. More specifically, it's data organization in documents and fields of a table. Consider the following points:

  - **Index topology**. Evaluate whether to colocate all data in a single index or distribute across multiple indexes. This decision significantly affects query performance, index maintenance, query simplicity, and dissimilar field configuration (or schema) between documents.

    For example, consider user queries that request content in a specific language. The simplest data design choice is possibly translating all languages into one language and storing it in a single index. Or, data can be stored in all languages in a single index. This choice results in multiple documents for each language. The index's filtering capability could be used to restrict the results to the desired language. Alternatively, each index could contain the translated versions for a given language, expected in the query.

    In some situations, you might need multiple search indexes. This approach allows you to independently optimize each index for maximum relevancy from your search queries. For example, an HR employee handbook and a product maintenance manuals serve different purposes and audiences. By indexing them separately, you can tailor the schema and search queries for each, improving user experience. This approach can be complex to implement and requires an orchestrator to facilitate calls to each index. The orchestration component is described in [Application design for AI workloads on Azure](application-design.md).

  > [!NOTE]
  > The choice between the two topologies and the data segmentation strategy depends on workload requirements, use cases, and user expectations.
  >
  > Doing cross-index queries can be challenging and can affect search relevancy. In worst case scenarios, there might be manual sifting through results, deciding which ones fit the criteria. This process introduces latency and adds complexity. In contrast, a single index approach is simpler and more straightforward. Relevancy can be improved by using index capabilities such as filtering.
  >
  > In some cases, compliance considerations often lead to the need for separate indexes. For instance, if business requirements demand that data is isolated between Europe and America, multiple indexes might be inevitable.

  - **Document design**. Align your data design with expected user queries to optimize relevancy. Consider how each document should serve queries. For search indexes, prioritize relevant documents and narrow down to a small set of documents that's densely packed with relevant information.

  - **Field design**. Configure your index fields to support search performance and relevance. Your index fields should map to the document attributes you want to make searchable, retrievable, filterable, and sortable. They include embeddings, IDs, or any other data that can boost searching.

#### Index capabilities

Configure the search index fields to return the most relevant set of documents. The decision depends on the capabilities that the search index technology and workload requirements support.

- **Filter, search, sort options**. Consider these options because they're directly related to use cases for augmentation. For example, _filterable_ determines true or false against a value provided in the query and returns relevant documents. For _searchability_, the attribute indicates whether the search query can reference the field. For instance, you might check if a text field contains specific text or if it's mathematically related to another vector. Optionally a relative weight can be assigned to that field as part of the search query. Result sets can also be _sortable_, where the result is listed by relevance.

  > ![Consider the tradeoff associated with adding index field capabilities.](../_images/trade-off.svg) **Tradeoff**. Enabling capabilities to index fields increases space requirements, affecting costs. Only add capabilities that you intend to use.

- **Metadata**. Indexes typically have metadata associated with index fields. Metadata helps us understand and manage data by providing relevant details about it. When designing indexes, consider whether metadata are retrievable or only used for relevance determination. The decision affects compute costs because the underlying indexing process is different. Excessive metadata can unnecessarily increase the size of the index.
  
There are many technology choices for indexing, many share similar characteristics, such as the ones listed previously. Some indexes might have extra features, like processing text and language analysis during indexing. To make text more suitable for indexing and searching, break text into tokens, convert it to lowercase, or remove stop words.

#### Efficient querying

Grounding data is used in generative AI applications to increase the accuracy and relevance of the responses to user queries. So consider user queries upfront. Understand what questions could be asked, who asks them, and how frequently. This helps the application form context and understand what result might be relevant.

Typical types of searches are:

- **Vector queries** search for similar items based on their vector representations or data points in a high-dimensional space.
  
- **Keyword search** searches within the entire content of textual documents. It indexes and queries large volumes of text data and is commonly used in search engines, databases, and document management systems.
  
- **Semantic ranking** improves the relevance of search results by reordering them based on their semantic relevance to the query, promoting the most semantically relevant matches to the top of the list.
  
- **Hybrid search** combines different search types, like vector search, full text search, and semantic ranking to further improve the relevancy of the search results.

To further improve model performance, combine search types.

The manner in which data is stored and processed affects query efficiency. Each time data is added to an index, compute cycles are needed for indexing. If indexing and responding to queries are done on the same compute resources, there could be contention. Ideally, an index should focus on the primary goal of answering queries efficiently and finding relevant documents rather than excessive indexing.

Cost and performance are key drivers of index design. Techniques like creating shadow copies can speed up querying. However, data duplication occurs through indexes, which incurs costs.

> ![Consider the tradeoff associated with adding field capabilities.](../_images/trade-off.svg) **Tradeoff**. Index design should consider both cost and performance. Strike a balance by optimizing storage and prioritizing efficient query answering and relevant document retrieval over excessive indexing.

For technology choices for the data store, search indexes, such as Elasticsearch or AI Search, provide powerful search capabilities, including vectorized and relevancy-based searches. Alternately, consider database options that support the type of data that you have and the types of queries you need because they're optimized for querying. Ultimately, it's about capabilities offered by the options and investment of building new skill set on the team.

## Data preparation

Grounding data is based on existing data, which needs to be made suitable for semantic querying. Some queries to find relevant document in the index can be literal matching. Other queries require fuzzy matching.

Before contextual data is ready to support inferencing requests to the model, there's an upfront preprocessing step that aims at cleaning, transforming, and structuring data. The goal is to reduce noise and bias, search efficiently, and maximize relevancy of the index searches. The choice tools or logic for preprocessing depends on workload team, but there are some broad considerations.

### Data volume rescoping

Data volume rescoping involves adjusting the scope of data, by expanding, or narrowing it to create a tight index so that relevance is increased. Query efficiency is another significant concern. Storing unnecessary data negatively affects both of those goals. For example, consider location data for a user. If only the city portion is relevant, optimize by storing just the city text instead of the full text that represents the address.

Here are some broad considerations.

- **Data elimination**. Retain only what's essential for the product's functionality, discarding unnecessary details. Here are some common examples.

  - **Qualitative elimination**. One way to transitioning from a broad scope to a narrower more relative one is to eliminate low-quality data by selectively only choosing to index relevant source data. The challenge lies in programmatically identifying content that's not relevant for AI scenarios. While the content might be useful for other intents, such as auditing or completeness, including it in the AI workload risks introducing lowering relevance. One way to flag such content is by using metadata that can be used at index population time if the content must be added to the index.
  
  - **Sensitive data**. Copying data from source data to an index might also bring over sensitive information. Respect the data classification labels applied at source and maintain the same level of sensitivity to this data set. When you deal with data that has personal information, don't store personal data unless you need it to respond to the query. For example, apply data classification when indexing emails. If an email is flagged as sensitive, avoid storing it in a general sensitivity data store.

  - **Normalizing and standardizing text**. Addressing typos and standardizing text is crucial for keyword-based indexes. A potential use case is translations, especially when dealing with multilingual content.
  
    This type of preprocessing also needed for embeddings, which enables you to compare words based on their context and significance. However, one challenge arises from the case sensitivity of words, context matters and there can be nuances, such as the semantic differences between the adjective "civic" and the proper noun "(Honda) Civic."

- **Data addition**. Augmenting context often relies on metadata, which isn't typically present in source data. For example, consider a text snippet. A human in the loop or AI creates relevant questions that can be answered using the context of the snippet. When you store these questions alongside the grounding data, user queries can be compared to the generated queries to evaluate document relevancy. Colocation of this new data with grounding data is a powerful way to enrich chunked data.

    Another use case is addition entities found while analyzing unstructured data. These entities can be added to the index and used for searching and filtering external systems or used to perform complex calculations. For instance, if we identify a company name, we might look up its industry or other relevant information from an external database and add that to our index.

  Consider maintaining data lineage. It's important for AI workloads to track the source of data because that information can get lost when a system aggregates various components into one index. This information might not be ever exposed to users, but information about data origins is crucial for internal data governance teams. This metadata isn't necessarily for the model but helps maintain transparency and accountability.

  > ![Consider the tradeoff associated with adding data.](../_images/trade-off.svg) **Tradeoff**. On one hand, adding new data increases the chances of finding relevancy within the dataset. However, this benefit comes at a cost. Specifically, the computational resources required to process and manage that  field. The time spent collecting and storing data can be substantial. Be aware of overloading with unnecessary fields can strain resources.

- **Processing text data**. Consider techniques such as synonyms, stemming, and semantic proximity to enhance relevancy. Delegate these techniques to tooling if possible. Some technologies, such as Elasticsearch or AI search, offer such features for preprocessing data during index creation.

#### Data type morphing

Index fields in a datastore are data-typed serving a specific purpose. Numeric fields facilitate efficient querying, text fields allow for text-based searches, and Boolean fields handle binary information.

Source data typically exists in various types of data, text, images, tables, and processing that data might be complex. You might have to extract key-value pairs, identify section headings for semantic chunking, recognize specific identifiers, and so on.  

For example, if your source data contains images, they aren't inherently searchable. They need to be converted into vector representations to enable efficient semantic searches and comparisons. If relevancy is tied to the data behind these formats, invest in extraction of the data. Transform source data types to functional data types that help in querying and analysis.

#### Chunking and embedding

Grounding data often contains a large volume of information while the model is able to tokenize only up to a certain amount. _Chunking_ is an important data design strategy because it involves dividing a document into smaller pieces that can be individually processed and indexed, allowing for efficient search and retrieval despite token limitations. Check the maximum number of tokens that your choice of large language model can handle. Your chunks shouldn't exceed that limit.

There are many techniques for implementing chunking. For more information, see [Chunking approaches](/azure/architecture/ai-ml/guide/rag/rag-chunking-phase#chunking-approaches).

_Embeddings_ is also another design strategy that enables vector search capabilities. Embeddings is a mathematical representation of an object generated by AI models  given some grounding data. They're stored in the index and add more context that helps complex queries yield results with better relevancy. For more information, see [Generate embeddings](/azure/architecture/ai-ml/guide/rag/rag-generating-embeddings).

## Index maintenance

Maintenance over time is one of critical aspects of index design. For static data, where documents remain unchanged, index maintenance is straightforward. But, most indexes are dynamic. Over time there might be new data added and the index schema might need new fields. Conversely, some data and fields might need to be deleted if they're no longer relevant. Commonly used technology options for indexers have features to handle updates automatically. For information about the recommended index characteristics, see [Considerations for a search index](data-platform.md#considerations-for-a-search-index).

### Maintenance criteria

- **Functionality updates**. The index might need to be updated if there's a change in application functionality. This situation happens when new questions are asked. To accommodate these changes, there might be a need to add new fields to the index or modify filtering, searching, or text-processing options on existing fields.

- **Data deletion**. Data deletion is challenging because you need to analyze available and missing data to determine what's irrelevant. To exclude outdated content from an index, consider the use of metadata that prevents search engines from indexing specific pages or content. Also, when choosing storage options, choose technology that support deletions efficiently. For example, blob storage supports soft deletes. If you use AI search and loading documents from storage, blob storage can detect removed documents and delete corresponding entries. This approach isn't ideal but is necessary when reindexing is costly because of a large index size.

  The concept of the _right to be forgotten_ refers to an individual's right to have their personal data removed from online platforms or databases. Ensure that you have policies in place to remove personal data if it was used for training. You can address this requirement by reindexing. If data is deleted from the transactional database, subsequent index updates reflect those changes.

- **Maintaining compatibility**. Applications often expect specific data structures, and any deviation can disrupt their functionality. For example, if a field is removed and the application requests that field, then there might be failure condition. Similarly to traditional database, adopt a forward compatibility mindset for indexes and maintain a level of rigor. When you make changes to the index, like adding or removing  fields, coordinate schema changes with code updates.

  > ![Consider the tradeoff of irrelevant data and cost.](../_images/trade-off.svg) **Tradeoff**. Add, updated, and delete actions against an index are expensive. Consider the frequency of updates and the cost to performance based on data store size and efficiency. Keeping obsolete documents in the index incurs storage, maintenance, and querying costs.

#### Deployment strategy

**Deployment strategy**. There are two main strategies for updating the index.

- **Side-by-side deployments**. In this approach, a new index that has updates lives alongside the existing one. After the new index is tested and fully operational, queries are switched to use the updated index. The application remains unaware of this switch because it only interacts with the new index. If other issues are discovered after the new index is deployed to production use, you can revert to the old index. This approach minimizes downtime and ensures continuous availability.

  Side-by-side updates work well when the cost of rebuilding the index is reasonable and can be completed in a reasonable time frame. In general, strive to keep indexes as efficient as possible because larger indexes consume more resources. Regularly monitor and maintain indexes to avoid unnecessary growth.

> [!TIP]
> When you perform resource-intensive data pre-processing tasks like entity recognition, lookups, and calculations, consider saving a copy of the results. This approach ensures that when you need to rebuild the index, you can avoid redoing all the computations. Some calculations might no longer apply because of deletions or updates, but many will remain relevant.

- **In-place update deployments**. This approach directly modifies the existing index. Saving the cost of duplication can be beneficial, but it also introduces risks because of potential downtime and resource-intensive operations. If your index is large and rebuilding it from scratch exceeds your desired update frequency, you can consider using in-place updates. However, this approach is challenging and carries the risk of breaching your service-level objective (SLO).

  > ![Consider the tradeoff between side-by-side deployments and in-place updates.](../_images/trade-off.svg) **Tradeoff**. Evaluate the cost of doing side-by-side deployments of indexes against doing in-place updates that deploy additions, updates, and deletions. In most cases, side-by-side updates are preferred over in-place updates. When an index is rebuilt, it effectively handles deletions and updates because the process creates a completely new data set. This strategy provides the opportunity to test data. Even though side-by-side deployments temporarily duplicate data and incur additional costs, the benefits in testing and performance evaluation often justify this storage requirement. Before making an index live, examine the data to ensure that it aligns with your expectations.

- **Scheduled updates**. Instead of maintaining continuous real-time communication with data sources, grounding data can be refreshed periodically. This approach ensures that the data stays relevant through scheduled updates, which eliminates the need for constant interaction.

- **Emergency updates**. Unexpected situations can arise, such as unwanted data inadvertently leaking into the search index. When this issue occurs, you might need to take immediate action, such as removing specific documents or adjusting data within the index. Regardless of your chosen model, such as side-by-side updates or in-place updates, always plan for the possibility of emergency operations.

- **Self-updating index**. If your indexing technology supports automatically updating the index to keep it synchronized with an external data source, it might be able to automatically process changes in the data. Data changes include additions or deletions, without manual intervention. Keep in mind that every change triggers an operation in the index, which consumes resources. The index might remain responsive to queries, but its capacity for handling them could be reduced during the update process.

#### Freshness operations

The time window between source data creation or modification and its addition to the index should be measured as an indicator and tracked against the SLOs. This indicator drives data decisions around updating your data pipeline design to ensure that data is available in your index when you need it. An index should only be as fresh as is required.

To maintain freshness, you can either rebuild the index entirely, or incrementally update it to stay synchronized with the original data sources. Both methods ensure that the index remains current and accurate.

Upfront investment in fine tuning the model might be less expensive than implementing RAG pattern, prompt engineering, and data augmentation methods.
