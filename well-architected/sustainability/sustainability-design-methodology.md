---
title: Design methodology for sustainability workloads on Azure
description: Understand the architectural process of building a sustainable application on Microsoft Azure.
author: PageWriter-MSFT
ms.author: prwilk
ms.date: 06/25/2026
ms.topic: concept-article
---

# Design methodology for sustainable workloads on Azure

Sustainable workload on Azure requires a deliberate engineering approach. Cloud resources consume energy in direct proportion to how they are provisioned and used, and architectural decisions across application, data, and infrastructure layers determine both utilization efficiency and environmental impact.

The sustainability design methodology provides a framework to measure, understand, and reduce that impact. It helps you make informed design decisions that improve carbon efficiency while meeting core requirements for reliability, security, performance, and cost. When tradeoffs become unclear under real-world constraints, return to this methodology as a reference point. It keeps design decisions grounded in efficiency and ensures sustainability is consistently considered in every architectural choice.


## Build carbon-aware applications

Design applications to reduce carbon emissions by minimizing energy consumption and adapting workload execution to the carbon intensity of the underlying energy supply.

Start by ensuring the application is both carbon and energy efficient. This means eliminating unnecessary computation, reducing data movement, and avoiding wasted processing so that each unit of work delivers meaningful value with minimal energy use. Even when a workload is cost optimized, it may still consume excess energy if inefficiencies remain in application design, data handling, or execution patterns.

Extend this by making workloads aware of carbon conditions. Design systems that can respond to variations in energy supply by shifting or shaping demand. Use demand shifting to move non-urgent or batch workloads to times or regions where electricity is cleaner or more abundant. Use demand shaping to adjust application behavior in real time, reducing processing intensity or deferring optional functionality when carbon intensity is high.

## Classify your emissions

Understand where emissions are generated across your workload using established frameworks such as the Greenhouse Gas Protocol, which defines Scope 1, Scope 2, and Scope 3 emissions. Scope 1 covers direct emissions from your operations. Scope 2 covers emissions from the energy you consume. Scope 3 covers indirect emissions across the full value chain of a service or product, including upstream impacts such as supply chain and infrastructure manufacturing, and downstream impacts such as how products are used by customers and the energy consumed during usage.

From a design perspective, focus on how architectural decisions across compute, storage, and networking influence emissions throughout the workload lifecycle. This visibility helps you identify inefficiencies, optimize resource usage, and reduce unnecessary energy consumption.

For example, excessive bandwidth usage or large payload sizes increase energy consumption beyond the data center, affecting network transmission across multiple hops and extending all the way to end-user devices. Reducing data transfer and optimizing payload efficiency can therefore significantly lower emissions across the delivery chain. The same principle applies to compute efficiency, storage design, and application architecture decisions.

## Measure and track carbon impact

Build the capability to measure, estimate, and continuously track their carbon impact using standardized and proxy-based models.

Align with industry practices like Software Carbon Intensity (SCI) model, which provides a structured way to quantify emissions as a function of energy consumption, carbon intensity of energy sources, and embodied carbon across infrastructure. 

Because direct carbon measurement can be complex, workloads should also support proxy-based estimation models that use available cost signals. These proxies allow teams to approximate carbon impact and identify inefficiencies even when full emissions data is not available.

To be effective, carbon measurement must extend beyond infrastructure alone. It should account for how applications scale, how efficiently resources are utilized, and how performance characteristics correlate with cost and energy consumption. Poor utilization or disproportionate cost growth relative to performance is often an indicator of unnecessary energy use and avoidable emissions.

## Prioritize carbon elimination before compensation

Design workloads with a clear understanding of how carbon emissions are reduced, ensuring that elimination is always the primary strategy before considering compensation approaches.

Sustainable software design follows a hierarchy of reduction methods. The most effective approach is elimination, which focuses on preventing emissions at the source by avoiding unnecessary computation, improving resource efficiency, and eliminating waste in application design and infrastructure usage. 

Once emissions have been minimized through elimination, remaining emissions can be addressed through neutralization or compensation. These approaches do not remove the source of emissions but instead balance their impact through external mechanisms.


## Apply a shared responsibility mindset

Reducing emissions in the cloud is a shared responsibility between the cloud provider and the customer. The provider is responsible for the efficiency of the underlying infrastructure and data centers, while the customer is responsible for how efficiently those resources are used through application design, deployment, and operations.

There are three ways to address emissions: carbon avoidance, carbon neutralization, and carbon removal. Carbon avoidance is the primary goal of sustainable software design, focusing on preventing unnecessary emissions before they occur. Carbon neutralization offsets emissions after they are produced, while carbon removal aims to actively remove carbon from the atmosphere.

Deploying workloads on a cloud platform does not guarantee sustainability. Even in highly optimized data centers, inefficient application design or poor resource utilization can lead to unnecessary emissions. For example, provisioning resources far beyond actual usage results in idle capacity that still consumes energy and contributes to emissions.

To improve sustainability, focus on using provisioned resources efficiently through right-sizing, scaling appropriately, and improving overall workload utilization. The goal is to ensure that infrastructure capacity is used effectively rather than left underutilized.


## Promote sustainability as a success measure

Design and operate workloads where sustainability is treated as a first-class success metric alongside reliability, performance, security, and cost.

Sustainability should be embedded into engineering and operational goals, with clear accountability and measurable outcomes such as carbon efficiency, SCI improvements, and resource utilization. Teams should be incentivized to optimize workloads for reduced environmental impact, and sustainability outcomes should be tracked, reviewed, and recognized as part of standard delivery practices. 


## Evolve sustainability practices with the ecosystem

Design workloads and organizational practices to continuously improve by learning from advancements in sustainable software engineering, tooling, and industry standards.

Sustainability evolves with new measurement models, platform capabilities, and engineering practices. Teams should actively invest in learning, share proven optimization techniques, and maintain a feedback loop of best practices across workloads. This includes adopting new tools, updating design patterns, and incorporating insights from frameworks such as the Green Software Foundation as they mature.

## Next steps

Review the design principles for sustainability.

> [!div class="nextstepaction"]
> [Reliability recommendations](sustainability-reliability-recommendations.md)

<!-- Updated: 2026-03-21 for Azure Update 558278 -->


## Community resources

- Read more about the [Carbon Awareness principle](https://learn.greensoftware.foundation/practitioner/carbon-awareness) from the Green Software Foundation.

- Explore carbon emission measurement by reviewing [Software Carbon Intensity](https://learn.greensoftware.foundation/practitioner/measurement#the-sci-equation) (SCI) specification.