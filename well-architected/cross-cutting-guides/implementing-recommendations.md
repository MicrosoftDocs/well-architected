---
title: Use the Microsoft Azure Well-Architected Review assessment
description: A guide for implementing recommendations for Well-Architected Review and Advisor recommendations for continuous workload improvement.
author: ckittel
ms.author: martinek
ms.date: 10/05/2023
ms.topic: conceptual
---

# Use the Microsoft Azure Well-Architected Review assessment

The [Microsoft Azure Well-Architected Review](/assessments/azure-architecture-review/) is a self-assessment to help a workload team examine their workload through the lenses of the Azure Well-Architected Framework. It is made of approximately 60 questions that are rooted in the key recommendations provided in the pillars of the Well-Architected Framework. This tool also has the ability to pull in [Azure Advisor](/azure/advisor/) recommendations based on an Azure subscription or resource group.

At the end of the assessment, recommendations are generated with links into supporting material to help improve a workload's design. There is an option to export these recommendations into a file that can be used to incorporate the recommendations into the operational processes for continuous workload improvement.

## When to use the assessment

For greenfield workloads, it's recommended to perform the assessment as part of the initial design process, inputting the proposed proposed decisions. The guidance then acts as both a baseline and starts a feedback loop to refine the workload design. As you make additional design decisions and capture them in additional assessment milestones periodically.

Brownfield workloads should be examined as well as part of the continuous improvement cycle of the workload. Set a cadence, for example every four months, and create a new milestone to track how the workload design could continue to improve.

![Guidance Overview](../_images/guidance-overview.svg)

## Assess to receive and integrate recommendations

Assess the workload by completing the assessment. The recommendations for your current milestone are available on the assessment's guidance page. Export these recommendations by using the **Export to CSV** button. Having an *offline* copy allows you to start socializing and enabling prioritization activities on the generated recommendations. While some teams may find using the CSV file sufficient, it's recommended to bring these recommendations into the workload's backlog so they can be integrated into the workload's software development lifecycle (SDLC).

> [!TIP]
> To help build automation for backlog integration, there are example import scripts available at [DevOps Tooling for Well-Architected Recommendation Process](https://github.com/Azure/WellArchitected-Tools/tree/main/WARP/devops#readme). These scripts show one possible way to import the recommendations from the Well-Architected Review exported CSV into a Azure DevOps or GitHub project within your existing organization.

## Prioritize and implement recommendations

The workload owners and key stakeholders should prioritize the recommendations per the team's standard work prioritization process, factoring in applicability of the recommendation and any tradeoffs associated with a specific design decision. For example, assigning recommendations to a specific owner and making decisions to potentially postpone or dismiss a recommendation. Like all planned work, the recommendation should be tracked until resolved as part of the workload's SDLC.

## Monitor improvements

Over time, the workload will be evolving due to functionality changes, paying down or accruing technical debt, and making tradeoffs. Use the milestone feature of the assessment to track this change over time, using the prior milestone as a baseline. In addition to seeing the change over time in the [Microsoft Azure Well-Architected Review](/assessments/azure-architecture-review/), the workload's part of the subscription's [Azure Advisor](/azure/advisor/) score will likely be improving as well.

## Tips

- Always be signed in when taking assessments so that milestones can be generated.

  > [!WARNING]
  > Assessments are tied to a Microsoft Learn profile and cannot be transferred or accessed between them.

- Select the Azure subscription or resource group that contains the primary bulk of your workload. This will ensure that only the relevant Azure Advisor recommendations are included when exporting the CSV. It is not possible include more than one subscription or exclude resource groups.
- Use a meaningful name for the assessment, don't take the default value. It should include the workload's name.
- Use meaningful milestone names to indicate the point in time that you're evaluating the workload.
- Use the notes feature on questions and on recommendations to capture any specifics you'd like to discuss with the workload team.
- An alternative to answering the 60 questions across all five pillars in one assessment, consider taking the assessment one pillar at a time, staggered every month. Be sure to include the name of the pillar in the assessment's name.

## Get personalized support

Work with your [Microsoft Partner](https://appsource.microsoft.com/marketplace/partner-dir) or your account team to see how they can help you perform an assessment as a formal engagement. As part of that engagement, they can then provide further details on the recommendations, which can help you decide on applicability and prioritization for future remediation.

## Next step

> [!div class="nextstepaction"]
> [Perform a Microsoft Azure Well-Architected Review](/assessments/azure-architecture-review/)
