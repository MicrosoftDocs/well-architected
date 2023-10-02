---
title: Data classification recommendations
description: Learn about data classification and how to apply it to your workloads. Categorize data based on its sensitivity levels, information type, and scope of compliance so that you can apply the right level of protection.
author: PageWriter-MSFT
ms.author: prwilk 
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for data classification

**Applies to Well-Architected Framework Security checklist recommendation:**

|[SE:03](checklist.md)|"_Classify and consistently apply sensitivity labels on all workload data and systems involved in data processing. Use classification to influence workload design, implementation, and security prioritization._"|
|---|---|

Most workloads store various types of data. Not all are equally sensitive. **Data classification is a way to categorize data based on its sensitivity levels, information type, and scope of compliance**  so that you can apply the right level of protection. Protection can involve assigning access controls, retention policies to different information types, and so on. While the actual security controls based on data classification are out of scope for this guide, it provides recommendations on categorizing data based on the preceding set of criteria that are set by the organization. 

**Definitions** 

| Terms | Definition |
|---|---|
| Classification |A process to categorize workload assets by sensitivity levels, information type, compliance requirements, and other criteria provided by the organization. |
| Taxonomy | A system to organize classified data by using an agreed upon structure. Typically, a hierarchical representation of classification that has named entities to indicate categorization criteria.|
| Metadata | An implementation to apply taxonomy to assets.  |


## Key design strategies

Classification is a crucial exercise and often a forcing function for building a system of records. It also **helps you in right-sizing the security assurances and helps the triage team expediate discovery** during incident response. A prerequisite to the design process is having a **clear understanding whether data should be treated as confidential, restricted, public, or other sensitivity considerations**. It's also essential to determine the locations where all that data is stored, because it might be distributed across multiple environments. Data discovery is necessary to locate all the data. Without that knowledge, most designs adopt a middle ground approach, which may or may not serve the security requirements. Data might be overprotected resulting in cost and performance inefficiencies. Worse cases, it might not be protected enough, which will only add to the attack surface. 

Data classification is a cumbersome exercise and there are tools available that can discover data assets and suggest classification. However, don't just rely on tooling. **Have a process in place** where team members diligently do the exercise and **use tooling to automate** when practical. 

In addition to these best practices, refer to [Create a well-designed data classification framework - Microsoft Service Assurance](/compliance/assurance/assurance-create-data-classification-framework).

###  Understand organization-defined taxonomy

_Taxonomy_ is a hierarchical representation of classification that has named entities to indicate categorization criteria.

In general, there isn't a universal standard for classification or defining taxonomy. It's purely **driven by organization's motivation for protecting data**. It may be compliance requirements, promise to the workload users, and other criteria that are usually driven by business needs.

Here are some example classification labels for sensitivity levels, information type, and scope of compliance.

| Sensitivity | Information type | Scope of compliance |
|---|---|---|
| Public <br> General <br> Confidential <br> Highly Confidential <br> Secret <br> Top Secret <br> Sensitive <br> Business Confidential | Financial <br> Credit Card <br> Name <br> Contact Info <br> Credentials <br> Banking <br> Networking <br> SSN <br> Health fields <br> Date of Birth <br> Intellectual property <br> Personal data | HIPAA <br> PCI <br> CCPA <br> SOX <br> RTB |

As a workload owner, **rely on your organization to provide you with a well-defined taxonomy**. All workload roles must have a shared understanding of the structure, nomenclature, and definition of the sensitivity levels. Don't define your own classification system.  

### Define the classification scope

Most organizations have a diverse set of labels. 

:::image type="content" source="images/data-classification/labels.png" alt-text="Diagram that shows an example of an organization's sensitivity labels." lightbox="images/data-classification/labels.png":::

Clearly identify which data assets and components are in-scope and out-of-scope for each sensitivity level.  You should **have a clear objective on the outcome**. The objective could be quicker triage, accelerated disaster recovery, regulatory audits. Having a clear understanding of objectives makes sure you right-size your efforts for classification. 

Start with these simple questions and expand as necessary based on the complexity of the system:

- What's the origin of data and information type.
- What's the expected restriction based on access. For example, is it public information data, regulatory, and other expected use cases.  
- What is the data footprint, where all is data stored, how long should the data be retained.
- Which components of the architecture interact with the data. 
- How does the data move through the system.
- What information is expected in the audit reports.
- Do you need to classify preproduction data.

If you have an existing system, **take inventory of all data stores and components that are in scope**. On the other hand, if you're designing a new system, create a data flow dimension of the architecture and have an initial categorization per taxonomy definitions.  Classification applies to the system as a whole. It's distinctly different from classification of configuration secrets and nonsecrets.

**Be granular and explicit when defining the scope**. Suppose your data store is a tabular system. You want to classify sensitivity at the table level or even the columns within the table. Also extend classification to nondata store components that might be related or have a part in processing the data. For example, have you classified the backup of your highly sensitive data store? If you're caching user sensitive data, is the caching data store in scope? If you use analytical data stores, how is the aggregated data classified.

### Design according to classification labels

**Classification should influence your architectural decisions**. The most obvious area is your segmentation strategy, which should consider the varied classification labels. 

For example, the labels influence the traffic isolation boundaries. There might be critical flows where end-to-end TLS is needed, while other packets can be over HTTP. If there are messages transmitted over a message broker, certain messages might need to be signed.

**For data at rest, the levels will affect the encryption choices**. You might choose to protect highly sensitive data through double encryption. Even different application secrets might need to be controlled with varied levels of protection. You might be able to justify storing secrets in hardware security module (HSM) store, which offers higher restrictions. Compliance labels also dictate decisions about the right protection standards. For example, The PCI-DSS standard mandates the use of FIPS 140-2 Level 3 protection, which is available only with HSMs. In other cases, it might be acceptable for other secrets to be stored in a regular secret management store. 

If you need to protect data in use, you might need to incorporate confidential compute in the architecture.

**Classification information should move with the data as it transitions through the system**, across components of the workload. Data labeled as confidential should be treated as confidential by all components that interact with it. For example, make sure you protect personal data by removing or obfuscating it from any kind of application logs.

**Classification impacts the design of your report** in the way data should be exposed. For instance, based on your information type labels, Do you need to apply data masking algorithm for obfuscation as a result of information type label? Which roles should have visibility into the raw data versus masked data. If there are any compliance requirements for reporting, how is data mapped to regulations and standards? Having this understanding makes it easier to demonstrate compliance with specific requirements and generate reports for auditors.

It also impacts the data lifecycle management operations, such as data retention and decommissioning schedules. 

### Apply taxonomy for querying

There are many ways to apply taxonomy labels to the identified data. **Using a classification schema with metadata is the most common way **to indicate those labels. Standardization through schema makes sure reporting is accurate, minimize chances of variation, and avoid creation of custom queries. Build automated checks to catch invalid entries.

You can apply labels manually, programmatically, or use a combination of both. **The architecture design process should include design of the schema**. Whether you have an existing system or are building a new one, when applying labels, maintain consistency in the key/value pair.   

Keep in mind that **not all data can be clearly classified**. Make an explicit decision about how data can't be classified should be represented in reporting. 

The actual implementation depends on the type of resources. **Certain Azure resources have built in classification systems**. For example, Azure SQL Server has a classification engine, supports dynamic masking, and also can generate reports based on metadata. Azure Service Bus allows inclusion of message schema that can have attached metadata. When you design your implementation, evaluate those features supported by the platform and take advantage of them. Make sure metadata used for classification is isolated and stored separately from the data stores.

There are also **specialized classification tools** that can detect and apply labels automatically. These tools are connected to your data sources. Microsoft Purview has capabilities that can autodiscover. There are also third-party tools that offer similar capabilities. Discovery process should be validated through manual verification.

**Review data classification regularly**. Classification maintenance should be built into operations, otherwise stale metadata can lead to erroneous results for the identified objectives and compliance issues.

> ![Tradeoff icon](../_images/trade-off.svg) **Tradeoff**: Be aware of the cost tradeoff on tooling. Classification tools require training and can be complex. 

Ultimately, classification must roll up to the organization through central teams. Get input from them about the expected report structure. Also, take advantage of centralized tools and processes to have organizational alignment and also alleviate operational costs.

## Azure facilitation

Microsoft Purview unified Azure Purview and Microsoft Purview solutions to provide visibility into data assets throughout your organization. 

For more information, see [What is Microsoft Purview?](/purview/purview).

Azure SQL Database, Azure SQL Managed Instance, and Azure Synapse Analytics offer built-in classification features. Take advantage to discover, classify, label, and report the sensitive data in your databases.

For more information, see [Data Discovery & Classification - Azure SQL Database, Azure SQL Managed Instance, and Azure Synapse](/azure/azure-sql/database/data-discovery-and-classification-overview).

## Organizational alignment

Cloud Adoption Framework provides guidance for central teams about how to classify data so that workload teams can follow the organizational taxonomy. 

[What is data classification? - Cloud Adoption Framework](/azure/cloud-adoption-framework/govern/policy-compliance/data-classification)

## Related links

[Data classification & sensitivity label taxonomy - Microsoft Service Assurance](/compliance/assurance/assurance-data-classification-and-labels)

[Create a well-designed data classification framework - Microsoft Service Assurance](/compliance/assurance/assurance-create-data-classification-framework)

## Security checklist

Refer to the complete set of recommendations. 

> [!div class="nextstepaction"]
[Security checklist](checklist.md)
