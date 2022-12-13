---
title: SAP workload design principles
description: SAP workload design principles
author: stephen-sumner
ms.author: ssumner
ms.date: 01/15/2022
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
ms.custom: SAP
---

## Guidance overview

We built this guidance around the Azure Well-Architected Framework and its five pillars of architectural excellence. The table below lists each pillar and provides a general summary of the articles in this set.

| Well-architected framework pillar | Summary |
| --- | --- |
| Reliability |An SAP workload requires resiliency at the architecture layer. You’ll learn how to create an SAP application with high availability to process critical business data. |
| Security| An SAP workload contains critical business data. You’ll learn to secure your SAP applications with multiple security layers, including identity, access, input validation, data sovereignty, and encryption.|
| Cost Optimization | An SAP workload has several architecture layers and numerous resources supporting it. You’ll learn how to make sure your SAP application deployment meets performance expectations while reducing the total cost of ownership.|
| Performance Efficiency | An SAP workload needs to be high performing resources to meet productivity requirements. You’ll learn how to make sure that your SAP workload meets user demands while managing costs.|
| Operational Excellence | An SAP workload spends most of its lifecycle in operations. You’ll learn how to manage an SAP workload and to keep it running.|

## Reliability

A reliable SAP workload is both resilient and available. Resiliency is the ability to recover from failures and continue to function. Availability is uptime. High availability reduces SAP application downtime during critical maintenance and improves recovery from failures such as VM crashes, backend updates, major downtime, or ransomware incidents. Failures happen on-premises and in the cloud, so it’s important to design your SAP workload for resiliency and availability. Below we’ve outlined key reliability recommendations.

**Conduct a reliability assessment.**Before you can standardize the reliability of an SAP workload and improve areas of weakness, you need to assess its reliability. It’s critical to know how reliable an SAP workload is so steps can be taken to fix issues or solidify those configurations. We recommend conducting a reliability assessment on your SAP workload. The assessment asks you questions about your workload and provides specific recommendations to focus on. The assessment builds on itself, so you can track your progress without restarting every time. For the assessment, start an [Azure Well-Architected Review](/assessments/) and select “SAP on Azure” when prompted.

## Cost optimization

Microsoft makes significant investments in the fast evolution of its hardware to provide more value for less.  The frequent increase in Azure hardware capability provides regular opportunity for an SAP workload to optimize costs, eliminate waste, and improve technology. To align Azure and your SAP workload, we recommend creating a plan for each SAP workload. The plan should contain the objectives and motivations for the workload. Organizational objectives and investment priorities should drive cost optimization initiatives. Below are cost-optimization recommendations for your SAP workload.

## Security

SAP on Azure is delivered in the infrastructure as a service (IaaS) cloud model. Microsoft builds security protections into the service at the levels of the physical data center, physical network, physical host, and hypervisor. But you're responsible for areas above the hypervisor, such as the guest operating system for SAP. We recommend you regularly evaluate the services and technologies used to ensure your security posture evolves with the threat landscape. Below are security recommendations for consideration.

## Performance efficiency

Performance efficiency is about accelerating digital transformation with less. The goal is to get the most out of your SAP workload and meeting user demand without over or under provisioning resources. Inefficient performance can degrade user experience or inflate costs. Performance affects productivity for internal applications. It determines growth for public facing applications. Designing an SAP workload that can't meet user demand will slow the application. Overcompensating with too much compute power will drive up cost needlessly. These scenarios are avoidable with the right guidance. Below are recommendations to drive performance efficiency for your SAP workload.

# Operational excellence

Operational excellence is about creating efficient processes to support your SAP workload. Operations will be the longest phase of the SAP workload lifecycle, and teams must be equipped with operational best practices to manage the day-today tasks. Failure in operations will affect the other design areas and the overall success of the SAP workload. It’s critical to tailor your operations to support an SAP workload in operations. Below are recommendations to drive operational excellence.