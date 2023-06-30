---
title: Well-Architected checklist for reliability
description: Review a checklist for reliability in application design. Considerations include uptime (availability), high resiliency, low latency, and cost.
author: martinekuan
ms.author: martinek
ms.date: 12/08/2021
ms.topic: conceptual
---

# Well-Architected checklist for reliability

Reliable applications should maintain a pre-defined percentage of uptime (*availability*). They should also balance between high resiliency, low latency, and cost (*High Availability*). Just as important, applications should be able to recover from failures (*resiliency*).

## In-scope audience:

// list of people with clear accountabilitiy
// Scoping call


## General checklist

> [!div class="checklist"]
> - **R-01**: Define availability and recovery targets to meet business requirements.
> - **R-05**: Identify the possible failure points in the system.
> - **R-06**: Implement design patterns in the application that reacts to transient and non-transient failures with internal or external dependencies.
> - **R-07**: Develop a comprehensive monitoring plan that gives you a holistic observability of the workload.
> - **R-08**: Test the system by simulating failures.
> - **R-09**: Design a disaster recovery strategy to run the workload with reduced functionality.
> - **R-10**: Have a backup strategy.

### Best practices guides 

|Guide|Description|
|---|---|
|[Business metrics](/azure/well-architected/resiliency/business-metrics)|TBD|
|[Failure mode analysis](/azure/well-architected/resiliency/design-resiliency)|TBD|

## Infrastructure perspective

> [!div class="checklist"]
> - **R-02**: Check zone and regional availability for all components in the architecutre.  
> - **R-03**: Check the service limits of the services used in the architecutre.   
> - **R-04**: Calculate composite Service Level Agreement (SLA) considering the critical services and keeping in alignment with business requirements.
> - **R-05**: Identify the possible failure points in the system.
> - **R-06**: Implement design patterns in the application that reacts to transient and non-transient failures with internal or external dependencies.
> - **R-07**: Develop a comprehensive monitoring plan that gives you a holistic observability of the workload.
> - **R-08**: Test the system by simulating failures.
> - **R-09**: Design a disaster recovery strategy to run the workload with reduced functionality.
> - **R-10**: Have a backup strategy.

### Best practices guides 

|Guide|Description|
|---|---|
|[Availability zones](/azure/well-architected/resiliency/app-design)|TBD|
|[Failure mode analysis](/azure/well-architected/resiliency/design-resiliency)|TBD|
## Application perspective

> [!div class="checklist"]
> - **R-02**: Check zone and regional availability for all components in the architecutre.  
> - **R-03**: Check the service limits of the services used in the architecutre.   
> - **R-04**: Calculate composite Service Level Agreement (SLA) considering the critical services and keeping in alignment with business requirements.
> - **R-05**: Identify the possible failure points.
> - **R-06**: Implement design patterns in the application that reacts to transient and non-transient failures with internal or external dependencies.
> - **R-07**: Develop a comprehensive monitoring plan that gives you a holistic observability of the workload.
> - **R-08**: Test the system by simulating failures.
> - **R-09**: Design a disaster recovery strategy to run the workload with reduced functionality.
> - **R-10**: Have a backup strategy.

### Best practices guides 

Table.