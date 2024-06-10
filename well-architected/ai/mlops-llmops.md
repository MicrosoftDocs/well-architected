---
title: MLOps and LLMOps for AI workloads on Azure
description: MLOps and LLMOps for AI workloads on Azure for running AI workloads.
author: PageWriter-MSFT
ms.author: prwilk
ms.date: 04/15/2024
ms.topic: conceptual
ms.service: waf
ms.subservice: waf-workload-ai
---

# MLOps and LLMOps for AI workloads on Azure

- Fine Tunining of models
- Developing and training models
- Secure access for end users
- Verification of training system

- Secure data handling
- Deployment of newly trained models

- Operational consideration around verifying and testing a model
- Prompt Evaluation
- Monitoring (required)

## Kapil's (SME & area co-lead) seed material

TODO

## Aleksandra's (SME & area co-lead) seed material

### Client in the insurance industry
#### Intro

##### -	Situation:
The client is a large company operating on the insurance domain that has a Microsoft competitor (AWS) already offering their services to the customer. Therefore, Microsoft first needed to win the customer’s trust to expand the Data & AI pipeline. After having presented a micro PoC in Prompt Flow to the customer’s CIO and his manager team, the CIO decided to acquire 100 hours of CSA support to give a go to the 1st ever PoC based on Generative AI. The CIO also appointed a small team of developers aiming to land their first GenAI PoC and learn more about Microsoft technology.

##### -	Use cases:
1.	The 1st use case presented by the customer’s business and IT departments is about building an AI assistant for creating a financial report resulting from a tailored analysis of a specific company.
The AI-generated report should include both the extraction of relevant indicators and the analyst's view on the company's financial performance and its positioning in the competitors' landscape.
2.	Following the successful implementation of the 1st use case prototype, the customer decided to present  a 2nd use case, dedicated to Log Analysis, for which another IT team was asked to join in. 
The capabilities expected from AI are therefore, the following: an Error Log Analyst that must classify logs, according to 3 pre-defined categories and if not present in any of the categories, suggest a new one. Additionally, the job of this AI copilot is to re-write error logs in a more user-friendly way in order to be more accessible for the business users once classified.
3.	While working on the 2nd use case and the evolution of the 1st use case (supported by Microsoft partner), the customer recognized the need for a well-architected and scalable upskilling journey for their IT teams, supervised by the company’s leadership. The most relevant domains of upskilling cited by the customer were identified as agentic and multiagent scenarios for GenAI. The Account team and the customer’s trusted CSA started more concrete discussions about how and when, also introducing PTU as a potential solution driver as the needs for generative AI inside the company are growing. 
#### Considerations 
Use case 1 – Report generation assistant:

-	Due to the customer’s relatively low commitment to generative AI and Microsoft products at the beginning of the journey, we could not count on reaching to a trusted Microsoft partner to accelerate the workload. For us it meant to keep a PoC in-house and implement it by the efforts of 
1) a CSA Data & AI (for solution backend) and a CSA App Innovation (for solution frontend) and 
2) a group of developers from the customer’s IT team appointed by the customer’s CIO. 
-	The absence both of a Microsoft partner and a background on generative AI / MLOps or LLMOps on the customer side resulted in the need of combining upskilling and solution development during meetings with the customer. Arguments such as embedding, tokenization, AI Search, RAG, Entity Extraction, Ingestion Pipeline, AI Studio, Prompt Flow, LLM Evaluation, Virtual Machines etc. were introduced  --> Operational excellence
-	As the prototype’s backend was implemented in Azure AI Studio (that at the time of implementation was still in preview), we tried to maximize our transparency about the product’s potential issues and shortcomings. Therefore, in some situations, s.a. developing Evaluation flows in Prompt Flow or trying different compute configurations with/and without custom package maintenance, the meetings with the customer evolved into a collective learning (also benefitting the Microsoft CSAs involved). Moreover, to provide the maximum support for the solution development, we stayed in a continuous contact with the customer, responding to technical questions outside of agreed meetings as well. --> Reliability, Operational Excellence
-	As the customer was new to LLMOps, first prompts that were developed were rather long and cumbersome, which resulted in an elevated cost in terms of token consumption and time to response. As the customer became more skilled on prompt engineering, also by following our recommendations, the prompt size and the overall number of LLM calls were reduced to the more optimal one, leading the flow to execute in  ca. 1 minute instead of 2+ minutes.  Cost optimization
In addition to the cost optimization, by leveraging prompt engineering and the collaboration with SMEs on the customer side, we reached improvements in terms of LLM performance on requested topics. --> Performance
-	 In order to ensure the solution’s security, all customer’s cloud policies were respected and approved by GOSP, the provider managing the customer’s cloud infrastructure. For instance, OpenAI models were only deployed in Europe and the majority of resources even stayed in West Europe as region. To support this compliance model, we have organized periodic sync-s with GOSP and informed them about all necessary configurations. --> Security
-	Once the PoC was concluded, it was presented to the customer’s leadership and business teams, as well as empirically tested. Upon positive results of solution testing, the customer decided to engage a Microsoft partner to bring the solution prototype into production by expanding it, keeping Microsoft CSAs as trusted advisors. At the moment we are working on the prototype evolution, which includes improving the document chunking strategy, processing of slides with multimodal LLM and drafting a system of custom evaluation for every report indicator getting extracted from documents and slides provided. Moreover, we are currently evaluating different LLM options, s.a. GPTo, GPT-4 turbo and so on, that can be put into PTUs for guaranteeing the flow execution in a time frame that is acceptable for the end user.

Use case 2 – Error log analysis assistant:

-	For the Log Analysis use case, we have started from a more beneficial position in terms of customer commitment and trust. This meant that the customer already had a collaboration model in mind – from PoC to Microsoft and partner-driven project – and was more autonomous in working with Microsoft products thanks to a previously carried out brief upskilling.
-	Despite some of the core team from the use case 1 being present in the development team for the use case 2, we still opted for a brief upskilling on Generative AI, AI Studio, Prompt Flow and Assistant API to bring everyone on the same page. After a ½ day of such tailored upskilling in which the 1st results of log processing on sample data in Azure OpenAI playground were shown, we won more customer trust and motivation to kick off the project the week after. The customer also returned home with concrete ideas in mind.
-	The customer showed proactivity in designing the PoC architecture and is currently putting weekly effort in developing a custom Prompt Flow supporting the log processing. In parallel, at the managerial level, we have started PTU discussions to be able to scale in the future, once the PoC is concluded.

Upskilling journey:

-	The customer was recommended to approach upskilling granularly, therefore progressively understanding the needs of automation for every department involved in the upskilling program and their respective knowledge bases that could power the eventual RAG chatbots. The customer was advised to consider Microsoft-driven AI orchestration frameworks, such as Semantic Kernel and Autogen to support new scenarios. The customer is currently gathering business needs to design a tailored upskilling journey resulting in new projects.
#### Recommendations (Ways to achieve the best outcome)
-	Generative AI is not a guarantee of success, it is rather probabilistic and subject to variations in terms of response quality and overall stability. One should be transparent with the customer about this aspect and explore ways of making LLMs to act more deterministically: such as plugins, prompt engineering, application logics, well prepared and structured KBs, eventual human interventions.
-	A PoC on the generative AI domain needs to be well parametrized and designed in advance in order to build realistic expectations. Without an architectural diagram and supportive slides, the customer might mistake spoken or written words for something else that is not included in the PoC or project perimeter.
-	A PoC on the generative AI domain should be at least supervised by multiple solution areas in order to respect desired application and infrastructure standards. Therefore, generative AI is not only a Data & AI topic, it should rather be addressed in a cross-solution manner.
-	There is no “best” technology or orchestration framework for LLMOps ad hoc – there are options that should be explored and evaluated based on customer needs.
-	A PoC on the generative AI domain, once concluded, should evolve into a project assisted by a Microsoft partner while Microsoft CSAs should remain in the advisory role. It is not sustainable to continue running a full-scale project while only relying on the CSA support if the skills required for the project success are not fully present in the customer’s development teams.
-	When presenting the results of a PoC on the generative AI domain, it is crucial to talk about its evolution to provide a vision for the customer of what can be possible, emphasizing the need of partner support.  A useful way of creating a powerful vision is to prepare a high-level architecture design chart.
-	Upskilling materials are crucial to provide a theoretical foundation for the customer in order to be “intellectually” onboarded to the LLMOps area and gain some comfort in using the technology portfolio. If possible, hands-on tutorials (from Microsoft learning paths) or sample materials (sample prompt flows, Jupiter notebooks etc.) should be recommended to the customer in order to make conscious choices in using the technologies.
-	Tendentially, clients have more than 1 generative AI use case in their departments but only invest in the subsequent use cases after observing the success / the first positive outcomes of the first one. For this reason, it is important to demonstrate the potential of GenAI while also tuning the customer expectations in terms of effort / costs / project duration that must be agreed upon with the STU and ATU (OneMicrosoft approach).
### Client in public services
#### Intro
-	Situation:
Customer is a very fragmented, not very digitalized public service provider operating across the whole Italy that tendentially faces a low-education user base on their webpages. The customer reached out upon upskilling support for improving the performance of a RAG chatbot already operating customer’s information website. Later in the timeline, the customer discovered the need to scale up their AI-driven operations and centralize their governance process under a unified “GenAI platform” for the whole company. Microsoft CSAs from Data & AI / App Innovation / Infra solution areas were engaged to support this workload.
-	Use cases:
Every application on the generative AI domain should be governed by the GenAI platform that needs to be configured in terms of resource / cost & usage monitoring policies. The customer requested the possibility to use multiple LLMs (not only GPT) on the same platform while also being able to accelerate the development of new applications with predisposed APIs (s.a. institutional RAG service, institutional Entity Extraction service, institutional Completion service and so one). Moreover, at the infrastructure level the customer expects having smart load balancing and charge back logics applied to LLMs allowed on the platform.
#### Considerations
-	No similar platform building and configuration experience was present in the Italian subsidiary at the time of project kickoff.
-	The customer had at least 5 different projects that were considered eligible for onboarding on the GenAI platform. Every project was guided by a separate team that had different project requirements and expectations from the platform, which made the communication very complex.
-	The customer had very agile internal dynamics, s.a. changes in the leadership teams, re-definition of responsibility zones and so on, which slowed down the decision-making processes.
#### Recommendations
-	Supporting such an ambitious project requires constant time and organization efforts, as well as a well-articulated brainstorming & creativity capacities. In a situation of repeating intellectual challenges and communication efforts it is important to maintain a healthy boundary between what can and cannot be impacted on the customer’s side because no matter how well prepared the CSAs are, the customer might be unable to follow their advice due to internal company dynamics.
-	Continuous improvement and revisions of the platform are crucial to guarantee its success as the solution naturally evolves with time and as the customer better consolidates their ideas. It is important to have periodic checks up with the customer and their trusted partners to monitor changes and intervene, if necessary.


## Anurag's (SME & area co-lead) seed material

TODO

## Jose's (Azure Patterns & Practices engineering) seed material

TODO

## Chad's (Azure Patterns & Practices engineering) seed material

TODO
