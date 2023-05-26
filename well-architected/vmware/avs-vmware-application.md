---
title: Application platform considerations for Azure VMware Solution (AVS)
description: TBD.
author: PageWriter-MSFT
ms.author: prwilk
ms.date: 01/26/2023
ms.topic: conceptual
ms.service: waf
ms.subservice: waf-workload-avs
---

# Application platform considerations for Azure VMware Solution (AVS)


//TODO: Add an introduction later


## Align to business metrics

Azure provides Service Level Agreements (SLAs) for all its services. The composite SLA of your workload should include [SLAs for Azure infrastructure]() and SLAs defined for the application.  

**Impact**: _Reliability_

##### Recommendations

- Define Service Level Agreements (SLAs) for the application and its key use cases.
- Determine how much downtime is acceptable. Quantify that value as the 
Recovery time objective (RTO). 
- Determine the the maximum duration of data loss that's acceptable during outage. Quantify that value as the Recovery point objective (RPO).
- Document the business metrics in your strategy for backup and disaster recovery. 

## Choose a migration approach

Common approaches for migrating or modernizing to the cloud are: Rehost, Refactor, Rearchitect, Rebuild. Each requires careful rationalization by evaluating the pros and cons. 

**Impact**: _Performance Efficiency, Cost Optimization_

##### Recommendations

- **Use assessments** to get a complete picture of the impact to the application, infrastructure, and the adjustments needed in your business strategy. This exercise will help you  understand the workload, its dependencies, and system requirements. You will be better prepared for your migration wave planning.

    - > [Strategic Migration Assessment and Readiness Tool (SMART)](/azure/cloud-adoption-framework/plan/smart-assessment)
    - > [Azure VMware Solution migration assessment](/migrate/concepts-azure-vmware-solution-assessment-calculation)
    - > [Application assessment](/azure/architecture/serverless-quest/application-assessment) 

- **Is Azure VMware Solution the right choice?** 

    Your workload might be better suited for IaaS or PaaS services. Those services might be more cost effective and performant than migrating to Azure VMware Solution.

    The modernization approach or the process of updating current apps and data to a cloud-first model can meet your business needs at reduced costs. Evaluate the choices for application and at the Azure infrastructure level. 
    
    - **Application**: Choose modernization based on the purpose of the application, life expetancy, supportability, cost and SLAs. 

    - **Infrastructure**: Consider the cost of an Azure VMware Solution node against running applications in Azure native services. You can run as many workloads in Azure VMware Solution as you can fit in the static memory/storage/compute. However porting applications to Azure native can be cost effective than instantiating another Azure VMware Solution node. 

    The application assessment results can help you understand where servers should be optimally placed.

## Establish a security baseline

The Microsoft cloud security benchmark provides recommendations on how you can secure your cloud solutions on Azure. This security baseline applies controls defined by Microsoft cloud security benchmark version 1.0 to Azure Policy.

**Impact**: _Security_

##### Design recommendations

Apply the recommendations given in the **security baseline** to protect your workload. 

> [Azure security baseline for Azure VMware Solution](https://learn.microsoft.com/en-us/security/benchmark/azure/baselines/azure-vmware-solution-security-baseline)


## Identify dependencies

Not all components of the solution are equally critical. Some components can take down the system while others might lead to degraded experience.

**Impact**: _Reliability, Operational Excellence_

##### Recommendations

**Create a  dependency map** that identifies the critical dependencies. Maintain and check the accuracy of the map at a regular cadence. 

## Automate the environment for scalability

The environment should be able to expand and contract based on load. Handle those operations through automation. User input should be kept to a minimum to avoid typical errors caused by human in loop.

**Impact**: _Performance Efficiency, Operational Excellence_

##### Recommendations

- Define high and low threshold values by running performance tests. Observe Azure metrics for the percentage usage of cluster CPU, memory, and storage resources. 
- Set alerts on the threshold values and trigger your the auto-scale node event within in Azure VMware Solution private cloud. 

> [!NOTE]
> ![GitHub logo](../_images/github.svg)For an example implementation, see [Auto-Scale function](/azure-vmware-solution/tree/main/avs-autoscale).