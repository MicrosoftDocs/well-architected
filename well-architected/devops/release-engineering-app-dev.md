---
title: Release engineering app development
description: Understand application development in release engineering. Build systems so your teams can turn ideas into production-delivered software with minimal friction.
author: david-stanford
ms.author: robbymillsap
ms.date: 04/08/2022
ms.topic: conceptual
ms.service: architecture-center
ms.subservice: well-architected
products:
  - azure-kubernetes-service
---

# Release Engineering: Application Development

One of the primary goals of adopting modern release management strategies is to build systems that allow your teams to turn ideas into production delivered software with as little friction as possible. Throughout this section of the Well-Architected Framework, methods and tools for quickly and reliably delivering software are examined. You will learn about things like continuous deployment, integration strategies, and deployment environments. Samples are provided to help you quickly get hands-on with this technology.

However, release engineering does not start with fancy deployment software, multiple deployment environments, or Kubernetes clusters. Before examining how we can quickly and reliably release software, we need to first look at how software is developed. 

Not only has the introduction of cloud computing had a significant impact on how software is delivered and run, but it's also had a huge downstream impact on how software is developed. For example, the introduction of container technology has changed how we can host, scale, and deprecate software. That said, containers have also impacted things like dependency management, host environment, and tooling as we develop software.

This article details many practices that you may want to consider when building strategies for developing for the cloud. Topics include:

- Development environments, or where you write your code.
- Source control and branching strategies, how you manage, collaborate on, and eventually deploy your code.

## Development environments

When developing software for the cloud, or any environment, care needs to be taken to ensure that the development environment is set up for success. When setting up a development environment, you may consider questions like the following:

- How do I ensure that all dependencies are in place?
- How can I best configure my development environment to emulate a production environment?
- How do I develop code where service dependencies may exist with code already in production?

Based on the chosen deployment method and target application the answers to these questions, and the tools used for what is often referred to the "inner-loop" development process may differ. 

Package management solutions – for example – can help you manage dependencies in your development environment. When using containers, you may want to use a solution like Docker Desktop for your development environment instead. 
When it comes to effectively emulating a production environment you may choose to set up a range of dummy services locally. 

Some Linux tools are a handy solution to this, and these are now available through the Windows Subsystem for Linux on Windows machines too. 

Finally, tools like Bridge for Kubernetes allow you to debug code in your development environment while being connected to a Kubernetes cluster. This configuration can be helpful when working on containerized microservices. You can work on one service locally while services that you take a dependency on are spun up remotely.

### Examples of "inner-loop" development tools

A development team might choose some of the pieces of software below when developing a containerized application with a Kubernetes cluster as its deployment target.

- [Azure Artifacts](https://azure.microsoft.com/en-us/services/devops/artifacts/) is a package management solution that lets you host private packages as well as provide upstream connectivity to public package repositories for a variety of package management systems such as [NuGet](https://www.nuget.org/). 
- [Docker Desktop](https://www.docker.com/products/docker-desktop) is an application that provides a Docker environment on your development system. Docker Desktop includes not only the Docker runtime but application development tools and local Kubernetes environment. 
- The [Windows Subsystem for Linux](/windows/wsl/) provides a Linux environment on your Windows machines, including many command-line tools, utilities, and Linux applications.
- [Bridge to Kubernetes](/visualstudio/containers/bridge-to-kubernetes) allows you to run and debug code on your development system while connected to a Kubernetes cluster. This configuration can be helpful when working on microservice type architectures. 
- [Podman](https://developers.redhat.com/articles/podman-next-generation-linux-container-tools) is an open-source tool for working with containers.

## Source control

Source control management (SCM) systems provide a way to control, collaborate, and peer review software changes. As software is merged into source control, the system helps manage code conflicts. Ultimately, source control provides a running history of the software, modification, and contributors. Whether a piece of software is open-sourced or private, using source control software has become a standardized method of managing software development. 

As cloud practices are adopted and because so much of the cloud infrastructure is managed through code, version control systems are an integral part of infrastructure management.

Many source control systems are powered by Git. Git is a distributed version control system with related tools that allow you and your team to track source code changes during the software development lifecycle. Using Git, you can create a copy of the software, make changes, propose the changes, and receive peer review on your proposal. Modern source control software simplifies this peer review process and helps you see the exact changes that a contributor wants to make. 

Once the proposed changes have been approved, Git helps merge the changes into the source, including conflict resolution. If, at any point, the changes need to be reverted, Git can also manage rollback.

### Version Control and code changes

Beyond providing us with a place to store code, source control systems allow us to understand what version of the software is current and identify changes between the present and past versions. Version control solutions should also provide a method for reverting to the previous version when needed.

The following image demonstrates how Git and GitHub are used to see the proposed code changes.

:::image type="content" source="../devops/git-changes.png" alt-text="Image alt text" lightbox="../devops/git-changes-full.png":::

### Branches, Forks and Pull Requests

Using source control systems, you can create both branches in an existing repo and your own copies of the software. A copy of an existing repo is called a "fork". 

While branches are usually short lived and will eventually be merged back into a repo via a pull request, fork are usually around for longer. While they can also be merged back via a pull request, some forks develop independently from their origin repo.

You can review our documentation on [Forks and Branches](/azure/devops/repos/git/forks) to find out more about the differences and similiarties between them.

### Peer Review

As updates are made to software and infrastructure configurations, version control software allows us to propose these changes before merging them into the source. During the proposal, peers can review the changes, recommend updates, and approve the changes. Source control solutions provide an excellent platform for collaboration on changes to the software.

To learn more about using Git, visit the [DevOps Resource Center](/devops/develop/git/what-is-git).

### GitHub

[GitHub](https://github.com) is a popular source control system that uses Git. In addition to core Git functionality, GitHub includes several other features such as access control, collaboration features such as code issues, project boards, wikis, and an automation platform called GitHub actions.

### Azure Repos

Azure DevOps is a collection of services for building, collaborating on, testing, and delivering software to any environment. Azure DevOps Services include [Azure Repos](/azure/devops/repos), which is its source control system. Using Azure Repos includes unlimited free private Git repositories. Standard Git powers Azure Repos, and you can use clients and tools of your choice for working with them.

## Next steps

> [!div class="nextstepaction"]
> [Release Engineering: Continuous integration](./release-engineering-ci.md)
