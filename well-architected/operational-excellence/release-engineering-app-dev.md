---
title: Release engineering app development
description: Learn how to build systems so that your teams can turn ideas into production-delivered software with minimal friction.
author: martinekuan
ms.author: martinek
ms.date: 05/01/2023
ms.topic: conceptual
---

# Release engineering: Application development

One of the primary goals of adopting modern release management strategies is to build systems that let your teams turn ideas into production-delivered software with as little friction as possible. Throughout this section of the Well-Architected Framework, methods and tools for quickly and reliably delivering software are examined. You'll learn about continuous deployment, integration strategies, deployment environments, and more. Samples are provided to help you quickly get hands-on with this technology.

However, release engineering doesn't start with fancy deployment software, multiple deployment environments, or Kubernetes clusters. Before examining how we can quickly and reliably release software, we need to first look at how software is developed.

Not only has the introduction of cloud computing affected how software is delivered and run, it has also had a huge downstream effect on how software is developed. For example, the introduction of container technology has changed how we can host, scale, and deprecate software. That said, containers have also impacted dependency management, host environment, and tooling as we develop software.

This article details many practices to consider when building strategies for developing for the cloud, including:

- Development environments, or where you write your code.
- Source control and branching strategies, or how you manage, collaborate on, and eventually deploy your code.

## Development environments

When you develop software for the cloud or any other environment, care needs to be taken to ensure that the development environment is set up for success. When setting up a development environment, consider the following questions:

- How do I ensure that all dependencies are in place?
- How can I best configure my development environment to emulate a production environment?
- How do I develop code where service dependencies might exist with code already in production?

Based on the chosen deployment method and target application, the answers to these questions, and the tools used for what's often referred to the "inner-loop" development process, can differ.

Package management solutions, for example, can help you manage dependencies in your development environment. When you use containers, you can use a solution like Docker Desktop for your development environment instead.
When it comes to effectively emulating a production environment, you can choose to set up a range of dummy services locally.

Some Linux tools are a handy solution, and they're available on Windows machines too. You can use them by configuring the Windows Subsystem for Linux.

Other tools let you provide developers with a preconfigured environment that they can spin up whenever they need it, providing a consistent environment. [Azure Deployment Environments](/azure/deployment-environments/) helps you to emulate environments, such as sandbox, testing, staging, or production.

Finally, tools like Bridge for Kubernetes let you debug code in your development environment while being connected to a Kubernetes cluster. This configuration can be helpful when working on containerized microservices. You can work on one service locally while services that you take a dependency on are spun up remotely.

### Examples of inner-loop development tools

A development team might choose some of the following pieces of software when developing a containerized application with a Kubernetes cluster as its deployment target.

- [Azure Artifacts](https://azure.microsoft.com/services/devops/artifacts/) is a package management solution that lets you host private packages and provide upstream connectivity to public package repositories for various package management systems such as [NuGet](https://www.nuget.org/).
- [Docker Desktop](https://www.docker.com/products/docker-desktop) is an application that provides a Docker environment on your development system. Docker Desktop includes not only the Docker runtime but also application development tools and local Kubernetes environments.
- [Windows Subsystem for Linux](/windows/wsl/) provides a Linux environment on your Windows machines, including many command-line tools, utilities, and Linux applications.
- [Bridge to Kubernetes](/visualstudio/containers/bridge-to-kubernetes) lets you run and debug code on your development system while connected to a Kubernetes cluster. This configuration is helpful when working on microservice type architectures.
- [Podman](https://developers.redhat.com/articles/podman-next-generation-linux-container-tools) is an open-source tool for working with containers.
- [Microsoft Dev Box](/azure/dev-box/) provides a safe development environment for developers to create code, supported through virtual networks and RBAC permissions that control who can access the organizations network and who can use the dev box.
- [Azure deployment environments](/azure/deployment-environments/) empowers development teams to quickly and easily spin up app infrastructure with project-based templates that establish consistency and best practices while maximizing security.

## Source control

Source control management (SCM) systems provide a way to control, collaborate, and peer review software changes. As software is merged into source control, the system helps manage code conflicts. Ultimately, source control provides a running history of the software, modifications, and contributors. Whether a piece of software is open-sourced or private, the use of source control software has become a standardized method of managing software development.

As cloud practices are adopted, and because so much of the cloud infrastructure is managed through code, version control systems are an integral part of infrastructure management.

Many source control systems are powered by Git. Git is a distributed version control system with related tools that lets you and your team track source code changes during the software development lifecycle. When you use Git, you can create a copy of the software, make changes, propose the changes, and receive peer review on your proposal. Modern source control software simplifies this peer review process and helps you see the exact changes that a contributor wants to make.

Once the proposed changes have been approved, Git helps merge the changes into the source, including conflict resolution. If at any point the changes need to be reverted, Git can also manage rollback.

### Version control and code changes

Beyond providing a place to store code, source control systems let you understand what version of the software is current and to identify changes between the present and past versions. Version control solutions also provide a method for reverting to the previous version when needed.

The following image demonstrates how Git and GitHub are used to see the proposed code changes.

:::image type="content" source="../operational-excellence/git-changes.png" alt-text="Screenshot of proposed code changes to a file on GitHub." lightbox="../operational-excellence/git-changes-full.png":::

### Branches, forks and pull requests

When you use source control systems, you can create branches in an existing repo and your own copies of the software. A copy of an existing repo is called a "fork".

While branches are usually short lived and will eventually be merged back into a repo via a pull request, forks usually remain for more time. While they can also be merged back via a pull request, some forks develop independently from their origin repo.

You can review our documentation on [Forks](/azure/devops/repos/git/forks) and [Branches](/azure/devops/repos/git/branch-policies-overview) to find out more about the differences and similarities between them.

### Peer review

As updates are made to software and infrastructure configurations, version control software lets us propose these changes before merging them into the source. During the proposal, peers can review the changes, recommend updates, and approve the changes. Source control solutions provide an excellent platform for collaboration on changes to the software.

To learn more about Git, see [What is Git?](/devops/develop/git/what-is-git)

### GitHub

[GitHub](https://github.com) is a popular source control system that uses Git. In addition to core Git functionality, GitHub includes features such as access control, collaboration features such as code issues, project boards, wikis, and an automation platform called GitHub actions.

### Azure Repos

Azure DevOps is a collection of services for building, collaborating on, testing, and delivering software to any environment. Azure DevOps Services includes [Azure Repos](/azure/devops/repos), which is its source control system. When you use Azure Repos, you also receive unlimited free private Git repositories. Standard Git powers Azure Repos, and you can use clients and tools of your choice for working with them.

## Next steps

> [!div class="nextstepaction"]
> [Release Engineering: Continuous integration](./release-engineering-ci.md)
