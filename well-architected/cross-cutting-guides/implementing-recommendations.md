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

## Monitor improvements

Over time, the workload will evolve due to functionality changes, eliminating or accruing technical debt, and making tradeoffs. So, you should take a data-driven approach; leverage Azure built-in tools such as Azure Monitor and Azure Advisor; review their reports regularly and optimizing the environment accordingly. Use the milestone feature of the assessment to track this change over time, using the prior milestone as a baseline. You'll see the change over time in the [Azure Well-Architected Review](/assessments/azure-architecture-review/). The workload's component of the subscription's [Azure Advisor](/azure/advisor/) score will probably improve as well.

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
- Whenever your proposed solution or service costs additional money, clearly mention it.
- Start remediating issues which have the highest reward-effort ratio as they provide quick wins. Check below for further details.

## How to report assessment results and get buy-in
### Executive summary
Executive summary has to be understandable by all types of audiences, as well as give a 50,000-foot view of the whole thing. Address following questions:
- What is this report about? 
- What did you assess? (Pillars, etc.)
- How did you do the assessment? (Workshops, hands-on investigation, etc.)
- Why are you using Microsoft Azure? What are the key takeaways? What are your main issues? What have you done well?
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

### Detailed findings

This section outlines more details about the most important issues along with recommendations. For more clarity, associate each issue with a severity level, preferably with some color. Briefly, advise how to address the issues. For example, “critical” issues can be marked as red and they require immediate attention; otherwise, they could result in data breach/security incidents. Below, you can see an example. Note that workload owners and key stakeholders should prioritize the recommendations in accordance with the team's standard work prioritization process, factoring in the applicability of the recommendations and any tradeoffs associated with a specific design decision. For example, recommendations might be assigned to a specific owner, or a recommendation might be postponed or dismissed. Like all planned work, the recommendation should be tracked until it's resolved, as part of the workload's SDLC. 

![image](https://github.com/azarboon/well-architected/assets/21277296/730b92c3-c61a-4b5f-9289-bbdbcd107f64)

Then add a corresponding reward-effort matrix for the aforementioned issues. You may want to start addressing issues from button-right as it can provide quick wins. 

![image](https://github.com/azarboon/well-architected/assets/21277296/65d767ac-ad34-4da7-bc15-7b18c7bf3d71)

### Findings by Pillar

This section targets a technical audience.  Create a subsection for each of the pillars, and list all the discovered points (negative and positive). Be specific. Here are some examples: 
**Operational Excellence**
- Does not have a fully automated integration and deployment setup
- Has a limited process or mechanism to mitigate deployment risks
- Has codified and automated a few components/operations 

**Security** 
- Does not have any process or mechanism to audit, manage and minimize permissions
- Relies on a centralized identity provider, which makes it easier to audit and to secure user authentication.

### Remediation by pillar 

This section targets a technical audience.  List all the action points to address major issues from each pillar. Be specific. You can use recommendations and corresponding links that you got at the end of the assessment. Here are a few examples: 

**Operational Excellence** 
Tagging 
- Currently, resources have tags, but there is no mechanism to ensure consistency across all resources. You can achieve that by creating policies in Azure Policy

**Security**
Virtual Machines: 
- Enable vulnerability assessment. Azure has an integrated tool that can help you do that. Please note that this can result in additional costs.
- Enable Azure Endpoint Protection to help against malware

### Effort and reward

Make it clear that improvement is a matter of trade-off. One cannot make everything perfect, though gradual improvement is the most effective way to start improving. Then, provide a high-level reward-effort matrix for the current state, consistent with pillars and subsections addressed in the previous section. For example:

![image](https://github.com/azarboon/well-architected/assets/21277296/5abfe06b-446f-4da3-bb16-807ccf10edfc)

### Proposed roadmap

Recommend to fix critical issues as soon as possible. Then propose milestones, each composed of action points. For example,

** Milestone 1**
- Configure built-in logging and monitoring tools in Azure components.
- Configure and leverage Azure central governance tools, review them at least weekly, and tune the architecture accordingly.
- Address suspicious authentications/authorizations in AD review.

** Milestone 2**
- Automate security remedial actions with help of Azure Policy.
- Setup and follow a suitable version control working model.

### Dependency Chart 

Outline dependencies between proposed actions from the roadmap. Following figure is only for illustration and helps the readers to understand the relationship between the activities.  

![image](https://github.com/azarboon/well-architected/assets/21277296/d45e1dd4-c29d-4cc7-b2fb-576a5ba3290f)

## Get personalized support

Work with your [Microsoft partner](https://appsource.microsoft.com/en-us/marketplace/partner-dir) or your account team to learn how they can help you perform an assessment as a formal engagement. As part of that engagement, they can provide further details on the recommendations. These details can help you determine the applicability of recommendations  and how to prioritize them for remediation.

## Next step

> [!div class="nextstepaction"]
> [Complete an Azure Well-Architected Review](/assessments/azure-architecture-review/)
