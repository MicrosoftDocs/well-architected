---
title: Data design for AI workloads on Azure
description: Data layout considerations for running AI workloads.
author: PageWriter-MSFT
ms.author: prwilk
ms.date: 04/15/2024
ms.topic: conceptual
ms.service: waf
ms.subservice: waf-workload-ai
---

# DUMP ZONE


### TODO Chad's (Azure Patterns & Practices engineering) seed material


TODO: @chad. Please insert appropriate jargon throughout. 
- Feature engg
- Feature tables

#### Dynamic/online training (or updating grounding data)

This is where the model (or grounding data) stays up to date with new data as it comes in. You do this to avoid staleness issues in the model. This helps smooth out seasonal changes in data (for example). You do need to monitor input, because a bad run (missing data, corrupt data) can impact the model right away. Automated monitoring and testing the training jobs are critical since the window is expected to be short between model releases -- automation will be key. A bug in the online training can cause production issues.

This model is shipping to production frequently.

Great for personalization experiences (using daily user trends)

#### Static/offline training (or generating grounding data or fine-tuning)

Best for data that doesn't need more realtime inclusion. The model (or grounding data) will be stale until refreshed. While you do need to monitor training, it happens less frequently so doesn't need to be fully automated. You still need to scrub data and monitor input (distributions, quality, etc).  For example, you wouldn't want to train on winter activity but try to predict summer behavior.

This model is shipping to production infrequently.

#### Offline inferencing

We pre-calculate predictions and store them in look-up tables. Look up has minimal latency. This requires predictions for all possible inputs. Allows us to validate (or even tweak) predictions before being used in production.

TODO: What's our data modeling guidance here?

#### Online inferencing

We don't pre-calculate predictions, and call into the model to answer questions right away (data "is" the model).

TODO: What's our data design guidance here, if any?

### Collecting datasets

- TODO: What are the recommendations for gathering training or grounding data?

### Cleaning and organizing data

- TODO: What are the recommendations for cleaning and organizing data for training or grounding consumption?

### Building training sets

- TODO: What are the recommendations for building training datasets?

- Break data into training, validation, and test (cross-validation) sets.

- When do you use cross-validation vs when do you split data between train/validation?

### Vectorization

Any topics to talk about here?

- sparse representation for large data sets (vector storage efficiency)
- Ensure numbers that should be treated as strings are vectorized accordingly (e.g. a zipcode)
- one-hot encoding: <https://en.wikipedia.org/wiki/One-hot>
  - binary vector
  - small datasets with limited number of classes
  - high memory usage



 

