These instructions are for AI agents and must be followed when generating new content, updating existing content, or reviewing content and pull requests in this repository.

# Repo content

- This repository contains the source data for the Microsoft Well-Architected Framework published as official Microsoft documentation on Microsoft Learn.
- The files in this repository are used to generate the HTML files to publish in Microsoft Learn under the public Url: https://learn.microsoft.com/azure/well-architected.
- The source of the Well-Architected Framework articles are stored in this repository as Markdown files and some YAML files.

## What is the Azure Well-Architected Framework?

The Azure Well-Architected Framework is a design framework that can improve the quality of a workload by helping it to:

- Be resilient, available, and recoverable.
- Be as secure as you need it to be.
- Deliver a sufficient return on investment.
- Support responsible development and operations.
- Accomplish its purpose within acceptable timeframes.

## Goals of the Well Architected Framework

The primary objective of the Well-Architected Framework is to set you up for success when you deploy your workload on Azure.

- Successful implementation: A well-architected design leads to successful implementation. Given the breadth and depth of coverage in concepts, you're well-equipped to make informed decisions.
- Confidence in success: Proven assessments, seen on numerous workloads deployed on Azure, back the tenets of the framework.
- Understand tradeoffs and risks: The framework helps you understand that adopting the recommendations might require making choices against the other pillars. It highlights the tradeoffs and also the potential risks that you might want to address in the short term.
- Optimize over time: The framework is designed for iterative use and as a tool for continuous improvement. Measure the maturity of your workload against the guidance. Treat that evaluation as a moving score that evolves with your workload, ensuring that the design remains efficient and effective in meeting your business objectives.

## Pillars of the Well-Architected Framework

The framework is founded on the five pillars of architectural excellence, which are mapped to those goals. They are: Reliability*, Security, Cost Optimization, Operational Excellence, and Performance Efficiency.

Each pillar provides recommended practices, risk considerations, and tradeoffs. The design decisions must be balanced across all pillars, given the business requirements. The technical and actionable guidance is broad enough for all workloads and applies to a specific scenario. This guidance is centered on Azure.

## Well Architected Framework content types

The Well-Architected Framework is structured in a layered approach: pillars, workload, and service guides.

- Core Pillars documentation: The foundation of this framework lies in the pillars. If you don't have a comprehensive understanding of these pillars, the subsequent layers—the workload layer and service guides—might not be fully comprehensible.
- WAF Workloads: The workload layer represents how the pillars apply to a specific class of workload. Workload architecture is segmented based on utility, and each segment represents the prioritized or design areas. These design areas are specific to the workload class and serve as focal points for optimization.
- WAF Service guides: Service guides are instrumental in decision-making that's related to the individual Azure components of a workload. They offer the core features and capabilities of each service that are necessary to attain architectural excellence. These guides aren't configuration guides. Also, they aren't a compiled list of all features and capabilities. The intent is to highlight the utility of the features through Well-Architected pillar perspectives.
- WAF Design Guides: Design guides focus on specific technical and architectural topics that span across multiple services and workloads, such as disaster recovery, health modeling, data partitioning, and transient fault handling. They provide practical implementation guidance to help architects successfully implement these cross-cutting concerns, offering proven patterns and best practices that align with the Well-Architected Framework pillars while addressing specific technical challenges.

## Repository Facts

- The Markdown and YAML files are the data files that contain the source of the articles that conform the Microsoft Well-Architected Framework.
- These Markdown and YAML files get converted to HTML for presentation on Microsoft Learn
- This data gets published at https://learn.microsoft.com/azure/well-architected.
- This is not a repository for software development.

## Repository folder structure

- The root folder for documentation is "well-architected/"
- Service guides can be found under the "well-architected/service-guides/" folder. The name of the file correspond to the official name of the azure service that is the focus of the guide, in small-kamel nomenclature. For example the files:
  - "well-architected/service-guides/virtual-machines.md" is the service guide for Virtual Machines.
  - "well-architected/service-guides/azure-netapp-files.md" is the service guide for Azure NetApp Files
- The Core Pillars guidance is located in folders based on their names
  - For Reliability, the root folder is "well-architected/reliability"
  - For Security, the root folder is "well-architected/security"
  - For Cost Optimization, the root folder is "well-architected/cost-optimization"
  - For Operational Excellence, the root folder is "well-architected/operational-excellence"
  - For Performance Efficiency, the root folder is "well-architected/performance-efficiency"
- WAF workloads are located under folders that are unique to each workload
  - For the AI Workload the root folder is "well-architected/ai/"
  - For the Azure Virtual Desktop workload the root folder is "well-architected/azure-virtual-desktop/"
  - For the Azure VMware Workload the root folder is "well-architected/azure-vmware/"
  - For the Mission-critical the root folder is "well-architected/mission-critical/"
  - For the Oracle workloads the root folder is "well-architected/oracle-iaas/"
  - For the SAP Workload the root folder is "well-architected/sap/"
  - For the Software as a service (SaaS) Workload the root folder is "well-architected/saas/"
  - For the Sustainability Workload the root folder is "well-architected-pr/well-architected/sustainability/"

## Content facts

- The articles never use content that would violate copyrights.
- The content in this repository is grounded in Microsoft Azure technology.
- The content in this repository always leads to the success of the person reading it.
- The content in this repository does not lead to bad or risky decisions without warning the reader about them.
- The content in this repository helps an architect avoid regret in their solution design.
- The content in this repository avoids risky decisions without proper warnings.
- The content in this repository is truthful, even while being opinionated.

## Writing style

### Key Microsoft style guide principles (subset)

The following are essential style guidelines from the Microsoft style guide. This is a subset of the full guide, focusing on the most critical principles for Well-Architected Framework content.

- Get to the point fast. Start with the key takeaway. Put the most important thing in the most noticeable spot. Make choices and next steps obvious. Give people just enough information to make decisions confidently. Don't get in the way.
- Talk like a person. Choose optimistic, conversational language. Use short everyday words, contractions, and sentence-style capitalization. Shun jargon and acronyms. And never miss an opportunity to find a better word.
- Simpler is better. Everyone likes clarity and getting to the point. Break it up. Step it out. Layer. Short sentences and fragments are easier to scan and read. Prune every excess word.
- Use bigger ideas, fewer words Our modern design hinges on crisp minimalism. Shorter is always better.
- Write like you speak Be friendly and conversational. No. Robot. Words.
- Project friendliness Use contractions: it's, you'll, you're, we're, let's.
- Get to the point fast Lead with what's most important. Front-load keywords for scanning. Make customer choices and next steps obvious.
- Be brief Give customers just enough information to make decisions confidently. Prune every excess word.
- When in doubt, don't capitalize Default to sentence-style capitalization—capitalize only the first word of a heading or phrase and any proper nouns or names. Never Use Title Capitalization (Like This). Never Ever.
- Skip periods (and : ! ?) Skip end punctuation on titles, headings, subheads, UI titles, and items in a list that are three or fewer words. Save the periods for paragraphs and body copy.
- Remember the last comma In a list of three or more items, include a comma before the conjunction: Android, iOS, and Windows. (The comma that comes before the conjunction is known as the Oxford or serial comma.)
- Don't be spacey Use only one space after periods, question marks, and colons—and no spaces around dashes.
- Revise weak writing  Most of the time, start each statement with a verb. Edit out you can, there is, there are, and there were.

