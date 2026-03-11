---
title: Collaborate with workload and platform teams
description: See how an architect collaborates with the workload team during implementation. Learn about ongoing collaboration tasks like setting review checkpoints.
author: ckittel
ms.author: chkittel
ms.date: 11/24/2025
ms.topic: concept-article
ms.update-cycle: 1095-days
---

# Collaborate with workload and platform teams

Delivering architecture specifications isn't a one-time task. Architects should engage with the workload team throughout implementation.

## Continuous collaboration tasks

- **Provide clarity**. Be readily available to clarify specifications so implementation teams remain unblocked. Participate in iteration planning and team meetings to address potential blockers.

- **Advise on implementation sequencing**. Architects understand that the journey from design to a production-ready product is iterative. Recommend tackling high-risk components or critical path items first. This approach helps the workload team identify feasibility issues early and apply learnings to the rest of the workload.

- **Set implementation review checkpoints**. Workload teams should establish regular checkpoints to compare the implementation with the architectural specification. This practice helps ensure that the design is implemented according to the specification and that the specification meets the predicted requirements. This feedback loop can rectify design or implementation errors.

- **Communicate with stakeholders**. Architects have an established relationship with stakeholders and the business and have an intimate understanding of the workload. As a result, they're often in a good position to relay implementation team concerns or negotiate requirement changes.

- **Make environment recommendations**. Workload design often extends beyond designing for production and its disaster recovery. Production is just one of many environments a workload implementation team might need. Architects can also assist workload teams in right-sizing preproduction environments.

## Use a proof of concept (POC)

Architects use POCs in their designs to inform decisions about the design specifications for the workload architecture. These POCs can also provide insight into the feasibility of the actual workload implementation.

> :::image type="icon" source="../_images/risk.svg"::: **Risk:** A common pitfall is treating POC code as production-ready. POCs are often built without security, logging, or error handling to prove a specific concept quickly. Architects must be explicit that POC code is disposable and should not be copy-pasted into the production codebase. Instead, use the POC to learn, then write the production code using established engineering standards.

## Manage technical debt

Sometimes, to meet a deadline or other constraints, a team might incur technical debt. The architect's role is to ensure the debt is:

- **Intentional**: The debt is taken on deliberately, not accidentally.
- **Documented**: The debt is tracked in the backlog.
- **Repayable**: There is a cost-effective plan to address it in future iterations.

## Collaboration with platform teams

Some organizations split responsibilities between workload and platform teams, as seen in Azure landing zones. For workloads that will be partnering with platform teams to co-deliver solutions and value, it's important to collaborate with those teams.

Architects should advocate for *golden paths*; secure and automated ways to consume platform resources. If you don't collaborate with platform teams, your design might miss out on taking cost advantage of available platform-provided offerings or you might design a solution that violates platform-mandated constraints on workloads.

## Mentorship and knowledge sharing

An architect's value multiplies when they complement the team's skills. Use code reviews, design sessions, and "lunch and learns" to explain the *why* behind architectural decisions. This empowers the implementation team to make decisions that align with the overall vision.

## Next steps

> [!div class="nextstepaction"]
> [Architect's checklist](checklist.md)
