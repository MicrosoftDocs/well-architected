---
title: Azure VMware Solution (AVS) operational procedures
description: TBD8.
author: PageWriter-MSFT
ms.author: prwilk
ms.date: 01/26/2023
ms.topic: conceptual
ms.service: waf
ms.subservice: waf-workload-avs
---

# Azure VMware Solution (AVS) operational procedures

## Desired state configuration


 You understand how the choices and desired configuration of the platform are managed	You are using DSC to manage confguration drift from both Azure and AVS workloads
	You are using Azure Arc for DSC
	You are using a 3rd party tool for DSC



## Backup

(Revisit back up choices from application platform)

You are storing your backups in a different region	To protect against the unlikely event of a prolonged regional outage, you can protect your workloads by replicating them to an alternative Azure region.