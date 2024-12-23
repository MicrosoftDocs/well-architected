---
title: Design methodology for AI workloads on Azure
description: Learn how to implement a structured approach when you make architectural design decisions for AI workloads on Azure.
author: PageWriter-MSFT
ms.author: prwilk
ms.date: 10/31/2024
ms.topic: conceptual
---

# Design methodology for AI workloads on Azure

Designing AI workloads involves integrating code and data to replace deterministic behavior and to enable tasks like prediction, classification, and other functional goals. AI workload architecture is often complex and must be designed within business constraints. Azure Well-Architected Framework provides a solid foundation for architectural excellence, but you also need to consider AI-specific design principles.

This article presents a suggested design methodology centered around those AI principles, which systematically guide the design and optimization of solutions. When you're uncertain in decision-making, refer to this methodology to emphasize design direction through high-level AI principles.

If you're designing a capability or introducing an improvement, evaluate the change from the methodology perspective. Is safe user experience affected? Is it flexible enough to adapt to future innovations? Will it disrupt the experimentation flow? Another benefit of the methodology is collaboration with product owners and stakeholders to justify technical decisions.

## Design with an experimental mindset

When you design with an experimental mindset, the goal is to *achieve relevancy through iterative and statistically driven processes* based on real-world use cases.

Experimentation in AI involves continuous adjustments with measurable results against quality targets after each iteration. An experimentation loop is necessary during the initial model evaluation and ongoing refinement. The inner loop refines the model's predictive power in the development environment. The outer loop monitors production usage and can trigger further refinement or data preparation. Both loops rely on continuous monitoring and evaluation to identify improvements.

Not every experiment succeeds. Consider worst-case scenarios and have contingency plans for failed experiments.

## Design responsibly

When users interact with your AI system, *they place their trust in its ethical functionality*, despite the opaque logic of AI models. This trust holds you accountable for designing the system to prevent unethical behaviors such as manipulation, content toxicity, IP infringement, and fabricated responses.

Embed responsible AI in systems operations and the culture of the team. The practices must extend throughout the lifecycle of user interaction. It begins with the user's initial intent to use the system, continues through sessions, and even includes disruptions caused by system errors.

Content moderation is a key strategy in responsible design of generative AI, where requests and responses are evaluated in real time to ensure safety and appropriateness. As part of the experimentation loops, strive to make algorithms fair and inclusive to minimize bias. Bias can enter the system through actual sessions or when you gather feedback.

Ethical data management is central to responsible design. It involves careful decisions about when to use or avoid relying on user data. Users trust you to ensure that any personal information is either removed from the system or retained only with their consent. If retention is inevitable, ensure that data is protected with trusted technology for privacy and security.

## Design for explainability

AI model results must be explainable. They require *justification and tracing of data origins, inference processes, and the journey of data* from the source to the serve layer. In discriminative AI, you can justify your decisions with each step, but explainability in generative models can be complex. It's essential to document the decision-making process, both manually and through technical capabilities.

The objective of this principle is to ensure the system's transparency and accountability to gain the user's trust.

## Stay ahead of model decay

Model decay is a unique challenge in AI that significantly affects design decisions. The quality of AI model outputs can deteriorate over time without any changes to the code, sometimes even suddenly, because of changes in data or external factors.

Deterioration affects various aspects of the system and includes:

- Data ingestion speed.
- Data quality.
- Monitoring needs.
- Evaluation processes.
- Reaction times to correct issues.

We recommend early detection through a combination of automated processes for continuous monitoring and model evaluation. User feedback is also an effective method for identifying model decay.

Regardless of the signals used to identify model decay, the operations team must engage data scientists to promptly research and address potential decay issues.

## Design for adaptability

AI technological advancement and adoption are advancing at a rapid pace. Be aware that *what you build today might become obsolete quickly* and affect your design decisions and processes.

This principle emphasizes the need for *agility and flexibility* and recognition that some components might have a short lifespan. Adopt a *pause-and-think* approach, where thorough research of model discovery, programming libraries and frameworks, and processing technologies is key.

## Next step

Learn about design principles for building and operating AI workloads on Azure.

> [!div class="nextstepaction"]
> [Design principles](./design-principles.md)