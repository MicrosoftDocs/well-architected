---
title: Release Engineering Rollback
description: Review deployment rollback options in Azure release engineering, such as with Azure App Service, Azure Kubernetes Service (AKS), or Azure Resource Manager.
author: martinekuan
ms.author: martinek
ms.date: 03/14/2023
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
products:
  - azure-app-service
  - azure-kubernetes-service
---

# Release Engineering: Rollback

In some cases, a new software deployment can harm or degrade the functionality of a software system. When building your solutions, it is essential to anticipate deployment issues and to architect solutions that provide mechanisms for fixing problematic deployments. Rolling back a deployment involves reverting the deployment to a known good state. 
Rollback can be accomplished in many different ways. Several Azure services support native mechanisms for rolling back to a previous state. We recommend leveraging them where available and where it makes sense for your chosen deployment model. 
The following examples will help you design your rollback and/or recovery plan.

## Make use of Staged Deployments

If you use a staged deployment strategy, you deploy several versions of your application and use networking techniques to switch between them. This allows you to quickly revert to a previous version of the application if you detect a problem along the way. 

When using Azure App Service, you can utilize deployment slots to implement this. Deployment slots are running instances of the application, each with a separate host name. 
Slots can be used to stage and test applications before promoting to a production slot. A deployment slot can be created to hold the last known good instance of your application. In the event of an issue or problematic deployment, the production slot can be swapped with the known good slot to bring the application back to a known good state quickly.
The image below shows how this can be accomplished via an Azure DevOps pipeline.

![Image of Azure DevOps pipeline tests in the Azure DevOps portal.](../devops/app-service-slots.png)

**Learn more**

For more information on using Azure App Service deployment slots, see [Set up staging environments in Azure App Service](/azure/app-service/deploy-staging-slots)

## Apply desired state configuration

Depending on the resources in your workload, you may be able to use desired state configurations to automate and govern your deployment process. 

Kubernetes is among the solutions that allow you to express your deployment instructions as a desired state configuration. 
With Kubernetes you can use a deployment instruction to describe the particular workload running in the cluster. A deployment may declare that a workload consists of three replicas of a specific pod that should be running at all times. The deployment object creates a ReplicaSet and the associate Pods. When updating a workload, the deployment itself can be revised, which will generally roll out a new container image to the deployment pods. 
Assuming multiple replicas of the pods exist, this rollout can happen in a controlled and staged manner and in harmony with your workload's overall deployment strategy.

**Learn more**

For more information, see [Kubernetes Deployments](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)

## Work with deployment records

A deployment record is created when deploying Azure infrastructure and solutions with Azure Resource Manager (ARM) templates or other infrastructure as code solutions. 
When creating a new deployment, you can provide a previously known good deployment so that if the current deployment fails, the previous know good deployment is redeployed. There are several considerations and caveats when using this functionality. See the documentation linked below for these details.

:::image type="content" source="../devops/arm-deployments.png" alt-text="Image showing Azure Resource Manager Deployments in the Azure portal." lightbox="../devops/arm-deployments-full.png":::

**Learn more**

For more information, see [Rollback on an error to successful deployment](/azure/azure-resource-manager/templates/rollback-on-error)

## Restore a known good state

Many managed services have a concept of versioning with a built-in restore function.

Azure Logic Apps - for example - create a new version of the application whenever an update is made to it. Azure maintains a history of versions and can revert or promote any previous version. To do so, in the Azure portal, select your Logic App and choose **Versions**. Previous versions can be selected on the versions pane, and the application can be inspected both in the code view and the visual designer view. Select the version you would like to revert to, and click the **Promote** option and then **Save**.

:::image type="content" source="../devops/revert-logic-app.png" alt-text="Image showing Azure logic application version history." lightbox="../devops/revert-logic-app-full.png":::

**Learn more**

For more information, see [Manage logic apps in the Azure portal](/azure/logic-apps/manage-logic-apps-with-azure-portal)
