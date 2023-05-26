---
title: Azure VMware Solution (AVS) security considerations
description: TBD.
author: PageWriter-MSFT
ms.author: prwilk
ms.date: 01/26/2023
ms.topic: conceptual
ms.service: waf
ms.subservice: waf-workload-avs
---

# Azure VMware (AVS) security considerations

Security requirements are defined for this workload	The Microsoft cloud security benchmark provides recommendations on how you can secure your cloud solutions on Azure. This security baseline applies guidance from the Microsoft cloud security benchmark version 1.0 to Azure Policy.	10		Define security requirements for the workload

## Identity

Access to vCenter is enabled using a 3rd party identity provider.	Allows administrators to log into the Azure VMware Solution vSphere Client using their own credentials (user@domainname.com)	30		Configure external identity source for vCenter Server

Access to NSX-T is enabled using a 3rd party identity provider	Allows administrators to log onto Azure VMware Solution's vCenter Server using their own credentials (user@domainname.com)	30		Configure external identity source for NSX-T Data Center

Have you added an external identity provider for Azure VMware Solution vCenter and NSX-T?	CloudAdmin is the default superuser available through the portal.  An external identity provider should be configured to provide both admin and specific privilege accounts.
	20		NSX-T Data Center LDAP integration for role-based access control (RBAC)