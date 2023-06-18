---
title: Azure VMware Solution (AVS) security considerations
description: TBD10.
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

## Security

### Guest VM security monitoring

* Use [Azure Policy for Azure Arc-enabled Servers](/azure/azure-arc/servers/policy-reference) to audit and enforce security controls on Azure VMware Solution guest VMs. Some of the key policies are as provided below.
  * [Install the Azure Security agent on Windows Arc machines in order to monitor them for security configurations and vulnerabilities](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0367cfc4-90b3-46ba-a8a6-ddd5d3514878)
  * [Configure Arc machines to automatically create an association with the default data collection rule for Microsoft Defender for Cloud](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F30f52897-df47-4ca0-81a8-a3be3e8dd226)
  * [System updates should be installed on your machines](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff85bf3e0-d513-442e-89c3-1784ad63382b)