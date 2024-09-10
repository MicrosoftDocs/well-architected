---
title: Workload personas involved in AI workloads
description: Different personas work on different aspects of the workload. This article highlights their roles and responsibilities and interactions with each other. 
author: PageWriter-MSFT
ms.author: prwilk
ms.date: 04/15/2024
ms.topic: conceptual
ms.service: waf
ms.subservice: waf-workload-ai
---

# Workload personas involved in AI workloads

An organization has several roles that participate in workload functions. One team's responsibility might be training the ML model while another team might consume it. In other case, you have a team who consume a finished model and customize it.

Securing data could be coming from a workload or multiple workload teams.

- Data Engineer
- Data Scientist
- Researcher
- Software Developer
- Release Engineer
- IT Operations Engineer
- Product Manager
- User

## Joakim Åström's (SME & area lead) seed material
- Connect the E2E process (MLOps, LLMOps, pretrained AI) with personas - where in the process are the personas active. IMAGE=yes
- Connect personas with known example roles and example departments for customers to be familiar, and to get orientation of the personas - where they live in their org. IMAGE=yes
- Do not focus on outlier personas, apply 80/20 rule, and focus on the personas real customers have. No utopia personas - verify customer references.
- Provide user stories to give examples of stories with personas in use.
- Sort all use cases & personas within the AI umbrella: Pretrained AI, Discriminative AI, Generative AI. IMAGE=yes

## Jose's (Azure Patterns & Practices engineering) seed material

TODO

## Chad's (Azure Patterns & Practices engineering) seed material

TODO


Lefovers:

 2. Data Roles and Responsibilities
   
	Who inside the company is responsible and SME for this data? 
	
	Does the data set contain confidential data? Are some data sets restricted to certrain people within the company? Is the information about the role based access available in source system (like Sharepoint)? Is this information stored in extra index field to allow AAD driven filtering of content?
	

	
grounding data preprocessing is a data scientest / data engineer role, because it's coupled with embedding selection and model tuning. It's still primarily in the data scientest space.  Since foundation models are appealing to app dev teams, it's common for that "role" to be played by a non-speciailzed individual that just happens to be able to manage data (such as an app engineer). But as far as I'm concerned, to do it right, you're donning the data scientest hat for that process.