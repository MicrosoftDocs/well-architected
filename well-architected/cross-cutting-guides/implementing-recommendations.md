---
title: Complete an Azure Well-Architected Review assessment
description: Learn about Azure Well-Architected Review and Azure Advisor recommendations that can help you continuously improve your workload.
author: ckittel
ms.author: martinek
ms.date: 11/15/2023
ms.topic: conceptual
---

# Complete an Azure Well-Architected Review assessment

[Azure Well-Architected Review](/assessments/azure-architecture-review/) is a self-assessment that can help a workload team examine a workload from the perspective of the Azure Well-Architected Framework. It consists of approximately 60 questions that are based on the key recommendations provided in the pillars of the Well-Architected Framework. The assessment tool can also pull in [Azure Advisor](/azure/advisor/) recommendations for an Azure subscription or resource group.

At the end of the assessment, you get recommendations and corresponding links to supporting material that can help you improve your workload's design. You can export these recommendations into a file that you can use in your final report to incorporate the recommendations into the operational processes for continuous workload improvement.

## When to take the assessment

For greenfield workloads, we recommend that you perform the assessment during the initial design process, entering the proposed decisions. The guidance then acts as a baseline and starts a feedback loop that you can use to refine the workload design as you make additional design decisions and periodically capture them in additional assessment milestones.

Brownfield workloads should be examined as well, as part of the continuous improvement cycle of the workload. Set a cadence, for example every four months, and use milestones to track how the workload design can continue to improve.

![Diagram that shows an overview of the continuous improvement cycle.](../_images/guidance-overview.png)

## Receive and integrate recommendations

Assess your workload by completing the assessment. The recommendations for your current milestone are available on the assessment's guidance page. Export these recommendations by selecting the **Export to CSV** button. You can use the offline copy to share the recommendations and start to prioritize them. Although some teams might consider the CSV file sufficient, we recommend that you add the recommendations to the workload's backlog so they can be integrated into the workload's software development lifecycle (SDLC).

> [!TIP]
> [DevOps Tooling for Well-Architected Recommendation Process](https://github.com/Azure/WellArchitected-Tools/tree/main/WARP/devops#readme) provides example scripts that can help you create automation for backlog integration. These scripts show one way to import the recommendations from the Well-Architected Review CSV file into an existing Azure DevOps or GitHub organization.

## Proposed format for final report
### Executive summary
Executive summary has to be understandable by all types of audiences, as well as give a 50,000-foot view of the whole thing. Address following questions:
- What is this report about? 
- What did you assess? (Pillars, etc.)
- How did you do the assessment? (Workshops, hands-on investigation, etc.)
- Why are you using Microsft Azure? What are the key takeaways? For example, what are your main issues? What have you done well?
- What is your solution? How are you trying to overcome those issues?
- What are the milestones to improve the situation? Briefly summarize what each milestone will achieve.

### Methodology
- How did you do the assessment? Workshops, hands-on investigation, etc.

### Assumptions
List all the assumptions you have made that readers should be aware of:
- Which assumptions did you receive from the stakeholders?
- What is the priority of the pillars?
- Which workloads are covered? Do you have to deal with mission-critical workloads? If yes, what are SLAs, etc.?   Is there any recovery objective defined?
- List any other assumptions that readers have to be aware of.

### Summary of findings
It has to be understandable for a non-technical audience so avoid using jargon. List main positive aspects of the environment, team, operation or strategy. Then list the main things which need improvement. Summary of findings should be readable within 1-2 minutes. 

## Prioritize and implement recommendations

Workload owners and key stakeholders should prioritize the recommendations in accordance with the team's standard work prioritization process, factoring in the applicability of the recommendations and any tradeoffs associated with a specific design decision. For example, recommendations might be assigned to a specific owner, or a recommendation might be postponed or dismissed. Like all planned work, the recommendation should be tracked until it's resolved, as part of the workload's SDLC.

## Monitor improvements

Over time, the workload will evolve due to functionality changes, eliminating or accruing technical debt, and making tradeoffs. Use the milestone feature of the assessment to track this change over time, using the prior milestone as a baseline. You'll see the change over time in the [Azure Well-Architected Review](/assessments/azure-architecture-review/). The workload's component of the subscription's [Azure Advisor](/azure/advisor/) score will probably improve as well.

## Tips

- Azure Well-Architected Review is a teamwork which has to be done in collaboration with workload owners and key stakeholders. Make it clear that this process must be free of any kind of blaming or finger pointing. Ask the respondents to be as honest as possible because that's the only uncover the issues and to fix them. Don't blindly trust their answers because there is a high chance of misunderstanding especially if the respondent isn't technically savvy. For every answer, ask clarifying questions to make sure you share the same understanding.
- You should always sign in when you take assessments so that the tool can generate milestones.
  > [!WARNING]
  > Assessments are tied to a Microsoft Learn profile. They can't be transferred to or accessed by other profiles.

- Select the Azure subscription or resource group that contains the biggest portion of your workload. Doing so helps ensure that only relevant Advisor recommendations are included in exported CSV files. It's not possible include more than one subscription or to exclude resource groups.
- Choose a meaningful name for the assessment, not the default value. The assessment's name should include the workload's name.
- Use meaningful milestone names to indicate when you're evaluating the workload.
- Use the notes feature on questions and on recommendations to capture any specifics that you want to discuss with the workload team.
- Rather than answering the 60 questions across all five pillars in one assessment, consider taking the assessment one pillar at a time, staggered by month. Be sure to include the name of the pillar in the assessment's name.
- Whenever your proposed solution or service costs additional money, clearly mention it in your report. 

## Get personalized support

Work with your [Microsoft partner](https://appsource.microsoft.com/en-us/marketplace/partner-dir) or your account team to learn how they can help you perform an assessment as a formal engagement. As part of that engagement, they can provide further details on the recommendations. These details can help you determine the applicability of recommendations  and how to prioritize them for remediation.

## Next step

> [!div class="nextstepaction"]
> [Complete an Azure Well-Architected Review](/assessments/azure-architecture-review/)
