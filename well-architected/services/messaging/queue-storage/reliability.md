---
title: Queue Storage and reliability
description: Focuses on the Queue Storage service used in the Messaging solution to provide best-practice, configuration recommendations, and design considerations related to Reliability.
author: v-stacywray
ms.author: robbymillsap
ms.date: 01/19/2022
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
products:
  - queue-storage
categories:
  - messaging
  - management-and-governance
---

# Queue Storage and reliability

[Azure Queue Storage](/azure/storage/queues/storage-queues-introduction) is a service for storing large numbers of messages that you can access from anywhere in the world through authenticated calls using `HTTP` or `HTTPS`. Queues are commonly used to create a backlog of work to process asynchronously.

For more information about Queue Storage, reference [What is Azure Queue Storage?](/azure/storage/queues/storage-queues-introduction)

To understand how Azure Queue Storage helps maintain a reliable workload, reference the following topics:

- [Azure Storage redundancy](/azure/storage/common/storage-redundancy?toc=/azure/storage/queues/toc.json)
- [Disaster recovery and storage account failover](/azure/storage/common/storage-disaster-recovery-guidance?toc=/azure/storage/queues/toc.json)

The following sections are specific to Azure Queue Storage and reliability:

- Design considerations
- Configuration checklist
- Recommended configuration options
- Source artifacts

## Design considerations

Azure Queue Storage follows the SLA statements of the general [Storage Account service](https://azure.microsoft.com/support/legal/sla/storage/v1_5/).

## Checklist

**Have you configured Azure Queue Storage with reliability in mind?**

> [!div class="checklist"]
> - Since Storage Queues are a part of the [Azure Storage service](/azure/storage/common/storage-account-overview?toc=/azure/storage/blobs/toc.json), refer to the [Storage Accounts configuration checklist and recommendations for reliability](../../storage/storage-accounts/reliability.md).
> - Ensure that for all clients accessing the storage account, implement a proper [retry policy](/azure/architecture/best-practices/retry-service-specific#azure-storage).
> - Refer to the Storage guidance for specifics on [data recovery for storage accounts](/azure/storage/common/storage-disaster-recovery-guidance?toc=/azure/storage/blobs/toc.json).
> - For an SLA increase, use geo-redundant storage.
> - Use geo-zone-redundant storage (GZRS) or read-access geo-zone-redundant storage (RA-GZRS) for durability and protection against failover if an entire data center becomes unavailable.

## Configuration recommendations

Consider the following recommendations to optimize reliability when configuring your Azure Queue Storage:

|Recommendation|Description|
|--------------|-----------|
|For an SLA increase, use geo-redundant storage.|Use geo-redundant storage with read access and configure the client application to fail over to secondary read endpoints if the primary endpoints fail to respond. This consideration should be part of the overall reliability strategy of your solution.|
|Use geo-zone-redundant storage (GZRS) or read-access geo-zone-redundant storage (RA-GZRS) for durability and protection against failover if an entire data center becomes unavailable.|For more information, reference [Azure Storage redundancy](/azure/storage/common/storage-redundancy).|

## Source artifacts

To identify storage accounts using locally redundant storage (LRS), use the following query:

```sql
Resources
| where
    type == 'microsoft.storage/storageaccounts'
    and sku.name =~ 'Standard_LRS'
```

To identify storage accounts using V1 storage accounts, use the following query:

```sql
Resources
| where
    type == 'microsoft.storage/storageaccounts'
    and kind == 'Storage'
```

## Next step

> [!div class="nextstepaction"]
> [Queue Storage and operational excellence](operational-excellence.md)