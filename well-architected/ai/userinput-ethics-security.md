---
title: User input, ethics and security considerations for running AI workloads on Azure
description: AI workload operations on Azure.
author: PageWriter-MSFT
ms.author: prwilk
ms.date: 04/15/2024
ms.topic: conceptual
ms.service: waf
ms.subservice: waf-workload-ai
---

# User input, ethics and security considerations


- Recognizing that a bad user experience is taking place or about to take place
- Avoiding or reducing bad user experiences
- Avoiding scenarios where users can take control of a model
- Ethical use of large language and other models


## Minimize the risk of leaking PII information

Protect user privacy while using AI services. (General)
Don't expose  raw user data. (General)
If LLM is not secured properly with strong rules, then you can get into a situation where the LLM leaks information.

