---
title: Queue Storage and operational excellence
description: Focuses on the Queue Storage service used in the Messaging solution to provide best-practice, configuration recommendations, and design considerations related to operational excellence.
author: martinekuan
ms.author: martinek
ms.date: 01/19/2022
ms.topic: conceptual
products:
  - azure-queue-storage
categories:
  - messaging
  - management-and-governance
---

# Queue Storage and operational excellence

[Azure Queue Storage](/azure/storage/queues/storage-queues-introduction) is a service for storing large numbers of messages that you can access from anywhere in the world through authenticated calls using `HTTP` or `HTTPS`. Queues are commonly used to create a backlog of work to process asynchronously.

For more information about Queue Storage, reference [What is Azure Queue Storage?](/azure/storage/queues/storage-queues-introduction)

To understand how Azure Queue Storage promotes operational excellence, reference the following topics:

- [Monitoring Azure Queue Storage](/azure/storage/queues/monitor-queue-storage?tabs=azure-portal)
- [Best practices for monitoring Azure Queue Storage](/azure/storage/queues/queues-storage-monitoring-scenarios?tabs=azure-powershell)

The following sections are specific to Azure Queue Storage and operational excellence:

- Design considerations
- Configuration checklist
- Source artifacts

## Design considerations

Azure Queue Storage follows the SLA statements of the general [Storage Account service](https://azure.microsoft.com/support/legal/sla/storage/v1_5/).

## Checklist

**Have you configured Azure Queue Storage with operational excellence in mind?**

> [!div class="checklist"]
> - Since Storage Queues are a part of the [Azure Storage service](/azure/storage/common/storage-account-overview?toc=/azure/storage/blobs/toc.json), refer to the [Storage Accounts configuration checklist and recommendations for operational excellence](../storage-accounts/operational-excellence.md).
> - Ensure that for all clients accessing the storage account, implement a proper [retry policy](/azure/architecture/best-practices/retry-service-specific#azure-storage).
> - Refer to the Storage guidance for specifics on [data recovery for storage accounts](/azure/storage/common/storage-disaster-recovery-guidance?toc=/azure/storage/blobs/toc.json).

## Source artifacts

To identify storage accounts using V1 storage accounts, use the following query:

```sql
Resources
| where
    type == 'microsoft.storage/storageaccounts'
    and kind == 'Storage'
```

## Next step

> [!div class="nextstepaction"]
> [IoT Hub and reliability](../iot-hub/reliability.md)