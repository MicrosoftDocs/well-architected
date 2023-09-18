---
title: Recommendations for developing background jobs
description: Learn how to develop background jobs to help minimize the load on the application UI, which improves availability and reduces interactive response time.
author: claytonsiemens77
ms.author: csiemens
ms.date: 11/15/2023
ms.topic: conceptual
---

# Recommendations for developing background jobs

**Applies to: RE 06**

This guide describes the recommendations for developing background jobs.
Background jobs run automatically without the need for user interaction. Many applications require background jobs that run independent of the UI.

Some examples of background jobs include batch jobs, intensive processing tasks, and long-running processes, such as workflows. The application starts the job and processes interactive requests from users. For example, if an application needs to generate thumbnails of images that users upload, a background job can be performed to generate the thumbnail and save it to storage. The user doesn't have to wait for the process to complete. As another example, a customer places an order, which initiates a background workflow that processes the order. The customer continues to browse the web app while the background job runs. After the background job finishes, it updates the stored order data and sends an email to the customer to confirm the order.

Background jobs help minimize the load on the application UI, which improves availability and reduces interactive response time.

## Key design strategies

To choose which task to designate as a background job, consider whether the task runs without user interaction and whether the UI needs to wait for the task to complete. Tasks that require the user or the UI to wait while they run are typically not appropriate background jobs.

### Types of background jobs

Some examples of background jobs are:

- CPU-intensive jobs, such as mathematical calculations or structural model analysis.

- I/O-intensive jobs, such as running a series of storage transactions or indexing files.

- Batch jobs, such as nightly data updates or scheduled processing.

- Long-running workflows, such as order fulfillment or provisioning services and systems.

- Sensitive-data processing that transfers the task to a more secure location for processing. For example, you might not want to process sensitive data within a web app. Instead, you might use a pattern such as the [Gatekeeper pattern](/azure/architecture/patterns/gatekeeper) to transfer the data to an isolated background process that has access to protected storage.

### Triggers

Initiate background jobs with:

- [Event-driven triggers](#event-driven-triggers): An event, typically a user action or a step in a workflow, triggers the task.

- [Schedule-driven triggers](#schedule-driven-triggers): A schedule that's based on a timer invokes the task. The job can be scheduled on a recurring basis or for a single run.

#### Event-driven triggers

An action triggers an event-driven invocation that starts the background task. Examples of event-driven triggers include:

- The UI or a different job places a message in a queue. The message contains data about a previously performed action, such as a customer that placed an order. The background job monitors this queue and detects the arrival of a new message. It reads the message and uses the message's data as the input for the background job. This pattern is called [asynchronous message-based communication](/dotnet/architecture/microservices/architect-microservice-container-applications/asynchronous-message-based-communication).

- The UI or a different job saves or updates a value that's in storage. The background job monitors the storage and detects changes. It reads the data and uses it as the input for the background job.

- The UI or a different job makes a request to an endpoint, such as an HTTPS URI or an API that's exposed as a web service. As part of the request, the UI or job transfers the data that the background task requires. The endpoint or web service invokes the background task, which uses the data as its input.

Other examples of tasks that are suited to event-driven invocation include image processing, workflows, sending information to remote services, sending email messages, and provisioning new users in multitenant applications.

#### Schedule-driven triggers

A timer triggers a schedule-driven invocation that starts the background task. Examples of schedule-driven triggers include:

- A timer that runs locally within the application or as part of the application's operating system regularly invokes a background task.

- A timer that runs in a different application, such as Azure Logic Apps, regularly sends a request to an API or web service. The API or web service invokes the background task.

- A separate process or application starts a timer that invokes the background task after a time delay or at a specific time.

Other examples of tasks that are suited to schedule-driven invocation include batch-processing routines (such as updating related products lists for customers based on their recent behavior), routine data-processing tasks (such as updating indexes or generating accumulated results), data analysis for daily reports, data retention cleanup, and data consistency checks.

If you use a schedule-driven task that must run as a single instance, review the following considerations:

- If the compute instance that runs the scheduler, such as a virtual machine (VM) that uses Windows scheduled tasks, is scaled, then you are running multiple instances of the scheduler. Multiple instances of the scheduler can start multiple instances of the task. For more information, see [What does idempotent mean in software systems?](https://particular.net/blog/what-does-idempotent-mean)

- If tasks run longer than the period between the scheduler events, the scheduler might start another instance of the task while the previous task runs.

### Return results

Background jobs run asynchronously in a separate process, or even in a separate location, from the UI or the process that invoked the background job. Ideally, background jobs are *fire and forget* operations. Their runtime progress doesn't have an effect on the UI or the calling process, which means that the calling process doesn't wait for the tasks to complete. The UI and the calling process can't detect when the task ends.

If you require a background task to communicate with the calling task to indicate progress or completion, you must implement a mechanism. Some examples are to:

- Write a status indicator value to storage that's accessible to the UI or the caller task, which can monitor or check this value. Other data that the background task returns to the caller can be placed in the same storage.

- Establish a reply queue that the UI or caller monitors. The background task can send messages to the queue that indicate the status. Data that the background task returns to the caller can be placed in the messages. For Azure Service Bus, use the `ReplyTo` and `CorrelationId` properties to implement this capability.

- Expose an API or endpoint from the background task that the UI or caller can access to obtain status information. The response can include the data that the background task returns to the caller.

- Configure the background task to call back to the UI or caller via an API to indicate the status at predefined points or on completion. You can use events raised locally or a publish-and-subscribe mechanism. The request or the event payload can include the data that the background task returns to the caller.

### Partition background jobs

If you include background jobs in an existing compute instance, consider how these changes affect the quality attributes of the compute instance and the background job. Consider these factors to decide whether to colocate the tasks with the existing compute instance or separate them into a different compute instance:

- **Availability**: Background tasks might not need the same level of availability as other parts of the application, in particular the UI and parts that directly involve user interaction. Background tasks might have a higher tolerance for latency, retried connection failures, and other factors that affect availability because the operations can be queued. However, there must be sufficient capacity to prevent backed up requests that can block queues and affect the entire application.

- **Scalability**: Background tasks likely have different scalability requirements compared to the UI and the interactive parts of the application. You might need to scale the UI to meet peaks in demand. Outstanding background tasks can run during less busy times and with fewer compute instances.

- **Resiliency**:  If a compute instance that only hosts background tasks fails, it might not fatally affect the entire application. The requests for these tasks can be queued or postponed until the task is available. If the compute instance or tasks can restart within an appropriate interval, it might not affect the application users.

- **Security**: Background tasks might have different security requirements or restrictions compared to the UI or other parts of the application. Use a separate compute instance to specify a different security environment for the tasks. To maximize security and separation, you can also use patterns such as Gatekeeper to isolate the background compute instances from the UI.

- **Performance**: Choose the type of compute instance for background tasks that specifically matches the task's performance requirements. You might use a less expensive compute option if the tasks don't require the same processing capabilities as the UI. Or you might use a larger instance if the tasks require more capacity and resources.

- **Manageability**: Background tasks might have a different development and deployment rhythm compared to the main application code or the UI. To simplify updates and versioning, deploy background tasks to a separate compute instance.

- **Cost**: If you add compute instances to run background tasks, hosting costs increase. Carefully consider the tradeoff between more capacity and extra costs.

For more information, see [Leader Election pattern](/azure/architecture/patterns/leader-election) and [Competing Consumers pattern](/azure/architecture/patterns/competing-consumers).

### Conflicts

If you have multiple instances of a background job, they might compete for access to resources and services, such as databases and storage. This concurrent access can result in resource contention, which might cause service availability conflicts and harm the integrity of the data that's in storage. Resolve resource contention by using a pessimistic-locking approach. This approach prevents competing instances of a task from concurrently accessing a service or corrupting data.

Another approach to resolve conflicts is to define background tasks as a singleton, so that only one instance runs. However, this approach eliminates the reliability and performance benefits that a multiple-instance configuration provides. This disadvantage is especially true if the UI supplies enough work to keep more than one background task busy.

Ensure that the background task can automatically restart and that it has sufficient capacity to handle peaks in demand. Allocate a compute instance with sufficient resources, implement a queueing mechanism that stores requests to run when demand decreases, or use a combination of these techniques.

### Coordination

Background tasks can be complex and require multiple tasks to run. In these scenarios, it's common to divide the task into smaller discrete steps or subtasks that multiple consumers can run. Multistep jobs are more efficient and more flexible because individual steps are often reusable in multiple jobs. It's also easy to add, remove, or modify the order of the steps.

It can be a challenge to coordinate multiple tasks and steps, but there are three common patterns to guide your solution:

- **Decompose a task into multiple reusable steps**. An application might be required to perform various tasks of different complexity on the information that it processes. A straightforward but inflexible approach to implementing such an application is to perform this processing as a monolithic module. But this approach is likely to reduce the opportunities for refactoring the code, optimizing it, or reusing it if the application requires parts of the same processing elsewhere. For more information, see the [Pipes and Filters pattern](/azure/architecture/patterns/pipes-and-filters).

- **Manage the orchestration of the steps for a task**. An application might perform tasks that comprise many steps, some of which might invoke remote services or access remote resources. Sometimes the individual steps are independent of each other, but they're orchestrated by the application logic that implements the task. For more information, see [Scheduler Agent Supervisor pattern](/azure/architecture/patterns/scheduler-agent-supervisor).

- **Manage the recovery for task steps that fail**. If one or more of the steps fail, an application might need to undo the work that a series of steps performs, which together defines an eventually consistent operation. For more information, see [Compensating Transaction pattern](/azure/architecture/patterns/compensating-transaction).

### Resiliency considerations

Create resilient background tasks to provide reliable services for the application. When you plan and design background tasks, consider the following points:

- Background tasks need to gracefully handle restarts without corrupting data or introducing inconsistency into the application. For long-running or multistep tasks, consider using *checkpoints*. Use checkpoints to save the state of jobs in persistent storage or as messages in a queue. For example, you can store state information in a message that's in a queue and incrementally update this state information with the task progress. The task can be processed from the last known checkpoint instead of restarting from the beginning.

  For Service Bus queues, use message sessions for this purpose. With message sessions, save and retrieve the application processing state by using the [SetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate) and [GetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate) methods. For more information about designing reliable multistep processes and workflows, see [Scheduler Agent Supervisor pattern](/azure/architecture/patterns/scheduler-agent-supervisor).

- When you use queues to communicate with background tasks, the queues can act as a buffer to store requests that are sent to the tasks while the application is under higher than usual load. The tasks can catch up with the UI during less busy periods, and restarts don't block the UI. For more information, see [Queue-Based Load Leveling pattern](/azure/architecture/patterns/queue-based-load-leveling). If some tasks are more important than others, consider implementing the [Priority Queue pattern](/azure/architecture/patterns/priority-queue) to ensure that these tasks run first.

#### Messages

Configure background tasks that are initiated by messages or that process messages to handle inconsistencies, such as messages that arrive out of order, messages that repeatedly cause an error (*poison messages*), and messages that are delivered more than once. Consider the following recommendations:

- Sometimes you need messages to be processed in a specific order, like messages that change data based on the existing data value, for example adding a value to an existing value. Messages don't always arrive in the order that they were sent. Also, different instances of a background task might process messages in a different order due to varying loads on each instance.
  
  For messages that must be processed in a specific order, include a sequence number, key, or another indicator that background tasks can use to process messages in the correct order. For Service Bus, use message sessions to guarantee the correct order of delivery. It's more efficient to design the process so that the message order isn't important.

- Typically, a background task peeks at messages in the queue, which temporarily hides them from other message consumers. After the task successfully processes the message, it deletes the message. If a background task fails when it processes a message, that message reappears in the queue after the peek timeout expires. A different instance of the task processes the message, or the next processing cycle of the original instance processes the message.

  If the message consistently causes an error in the consumer, it blocks the task, the queue, and eventually the application itself when the queue becomes full. It's vital to detect and remove poison messages from the queue. If you use Service Bus, automatically or manually move poison messages to an associated [dead letter queue](/azure/service-bus-messaging/service-bus-dead-letter-queues).

- Queues are *at-least-once* delivery mechanisms, but they might deliver the same message more than once. If a background task fails after it processes a message but before it deletes it from the queue, the message is available for processing again.

  Background tasks should be idempotent, which means that when the task processes the same message more than once, it doesn't cause an error or inconsistency in the application's data. Some operations are naturally idempotent, for example if a stored value is set to a specific new value. However, some operations cause inconsistencies, for example if a value is added to an existing stored value without verifying that the stored value is still the same as when the message was originally sent. Configure Service Bus queues to automatically remove duplicated messages. For more information, see [Idempotent message processing](/azure/architecture/reference-architectures/containers/aks-mission-critical/mission-critical-data-platform#idempotent-message-processing).

- Some messaging systems, such as Azure Storage queues and Service Bus queues, support a dequeue count property that indicates how many times a message from the queue is read. This data is useful for handling repeated messages and poison messages. For more information, see [Asynchronous messaging primer](/previous-versions/msp-n-p/dn589781(v=pandp.10)) and [Idempotency patterns](https://blog.jonathanoliver.com/idempotency-patterns).

### Scaling and performance considerations

Background tasks must offer sufficient performance to ensure that they don't block the application or delay operation when the system is under load. Typically, performance improves when you scale the compute instances that host the background tasks. When you plan and design background tasks, consider the following points related to scalability and performance:

- Azure Virtual Machines and the Web Apps feature of Azure App Service can host deployments. They support automatic scaling, both scaling out and scaling in. The automatic scaling is determined by the demand and load or a predefined schedule. Use automatic scaling to help ensure that the application has sufficient performance capabilities while minimizing runtime costs.

- Some background tasks have a different performance capability compared to other parts of an application, for example the UI or components, such as the data access layer. In that scenario, host the background tasks together in a separate compute service so the UI and background tasks can scale independently to manage the load. If multiple background tasks have significantly different performance capabilities, divide them and scale each type independently. This technique might increase runtime costs.

- To prevent the loss of performance under load, you might also need to scale storage queues and other resources so a single point of the processing chain doesn't cause a bottleneck. Consider other limitations, such as the maximum throughput of storage and other services that the application and the background tasks rely on.

- Design background tasks for scaling. For example, background tasks must dynamically detect the number of utilized storage queues to monitor messages or send messages to the appropriate queue.

- By default, a WebJob scales with its associated Web Apps instance. However, if you want a WebJob to run as only a single instance, you can create a Settings.job file that contains the JSON data `{ "is_singleton": true }`. This method forces Azure to only run one instance of the WebJob, even if there are multiple instances of the associated web app. This technique is useful for scheduled jobs that must run as only a single instance.

## Azure facilitation

The following sections describe the Azure services that you can use to host, run, configure, and manage background jobs.

### Host environments

There are several Azure platform services that can host background tasks:

- [Web Apps and WebJobs](#web-apps-and-webjobs): Use the WebJobs feature of App Service to run custom jobs that are based on different scripts or programs that you can run in a web app.

- [Azure Functions](#azure-functions): Use function apps for background jobs that don't run for a long time. You can also use function apps if you host your workload on an underutilized App Service plan.

- [Virtual Machines](#virtual-machines): If you have a Windows service or want to use Windows Task Scheduler, host your background tasks in a dedicated VM.

- [Azure Batch](#batch): Batch is a platform service that you can use to schedule compute-intensive work to run on a managed collection of VMs. It can automatically scale compute resources.

- [Azure Kubernetes Service (AKS)](#azure-kubernetes-service): AKS provides a managed hosting environment for Kubernetes on Azure.

- [Azure Container Apps](#container-apps): With Container Apps, you can build serverless microservices that are based on containers.

The following sections provide considerations for each of these options to help you choose the best option for you.

### Web Apps and WebJobs

You can use the WebJobs feature to run custom jobs as background jobs in a web app. A WebJob runs as a continuous process in the context of your web app. A WebJob can also run in response to a trigger event from Logic Apps or external factors, such as changes to storage blobs or message queues. WebJobs can be started and stopped on demand, and shut down gracefully. If a continuously running WebJob fails, it's automatically restarted. You can configure retry and error actions.

When you configure a WebJob:

- If you want the job to respond to an event-driven trigger, configure it to **Run continuously**. The script or program is stored in the folder that's named site/wwwroot/app_data/jobs/continuous.

- If you want the job to respond to a schedule-driven trigger, configure it to **Run on a schedule**. The script or program is stored in the folder named site/wwwroot/app_data/jobs/triggered.

- If you choose the **Run on demand** option when you configure a job, it runs the same code as the **Run on a schedule** option when you start the job.

A WebJob runs in the sandbox of the web app. It has access to environment variables and it can share information, such as connection strings, with the web app. The WebJob has access to the unique identifier of the machine that runs the WebJob. The connection string named `AzureWebJobsStorage` provides access to Storage queues, blobs, and tables for application data. It also provides access to Service Bus for messaging and communication. The connection string named `AzureWebJobsDashboard` provides access to the WebJob action log files.

WebJobs have the following characteristics:

- **Security**: The deployment credentials of the web app provide protection for WebJobs.

- **Supported file types**: Define WebJobs by using command scripts (.cmd), batch files (.bat), PowerShell scripts (.ps1), Bash shell scripts (.sh), PHP scripts (.php), Python scripts (.py), JavaScript code (.js), and executable programs (.exe and .jar).

- **Deployment**: You can deploy scripts and executables by using the [Azure portal](/azure/app-service-web/web-sites-create-web-jobs), [Visual Studio](/azure/app-service-web/websites-dotnet-deploy-webjobs), or the [WebJobs SDK](/azure/app-service/webjobs-sdk-get-started), or you can copy them directly to the following locations:

  - For triggered deployment: site/wwwroot/app_data/jobs/triggered/\<job name\>

  - For continuous deployment: site/wwwroot/app_data/jobs/continuous/\<job name\>

- **Log files**: `Console.Out` is treated, or marked, as `INFO`. `Console.Error` is treated as `ERROR`. Use the portal to access monitoring and diagnostics information. Download log files directly from the website. Log files are saved in the following locations:

  - For triggered deployment: Vfs/data/jobs/triggered/\<job name\>

  - For continuous deployment: Vfs/data/jobs/continuous/\<job name\>

- **Configuration**: Configure WebJobs by using the portal, the REST API, and PowerShell. Use a configuration file named settings.job, which is in the same root directory as the WebJob script, to provide configuration information for a WebJob. For example:

  - `{ "stopping_wait_time": 60 }`

  - `{ "is_singleton": true }`

#### Web Apps and WebJobs considerations

- By default, WebJobs scale with the web app. To configure WebJobs to run on a single instance, set the `is_singleton` configuration property to `true`. Single instance WebJobs are useful for tasks that you don't want to scale or run as simultaneous multiple instances, such as reindexing or data analysis.

- To minimize the effect of WebJobs on the performance of the web app, create an empty web app instance in a new App Service plan to host long-running or resource-intensive WebJobs.

### Azure Functions

Azure Functions is similar to WebJobs. Azure Functions is serverless and is most suitable for event-driven triggers that run for a short period. You can also use Azure Functions to run scheduled jobs via timer triggers if you configure a function to run at specified times.

Azure Functions isn't recommended for large, long-running tasks because a function can cause unexpected timeouts. However, depending on your hosting plan, consider using functions for schedule-driven triggers.

#### Azure Functions considerations

If you expect the background task to run for a short duration in response to an event, consider running the task in the consumption plan. You can configure the runtime to a maximum time. A function that runs for longer costs more. CPU-intensive jobs that consume more memory can be more expensive. If you use additional triggers for services as part of your task, they're billed separately.

The premium plan is suitable if you have several tasks that are short but they run continuously. This plan is more expensive because it needs more memory and CPU. As a benefit, you can use other features, such as virtual network integration.

The dedicated plan is suitable for background jobs if your workload already runs on the dedicated plan. If you have underutilized VMs, you can run the dedicated plan on the same VM and share compute costs.

For more information, see:

- [Azure Functions hosting options](/azure/azure-functions/functions-scale)
- [Timer trigger for Azure Functions](/azure/azure-functions/functions-bindings-timer)

### Virtual Machines

You can implement background tasks so that they're not deployed to Web Apps. For example, you can implement tasks by using Windows services, third-party utilities, or executable programs. You can also use programs that are written for a runtime environment that's different than the environment that hosts the application. For example, you might use a Unix or Linux program that you want to run from a Windows or .NET application. Choose from several operating systems for an Azure VM, and run your service or executable on that VM.

For more information, see:

- [Choose an Azure compute service](/azure/app-service-web/choose-web-site-cloud-service-vm)
- [Sizes for VMs in Azure](/azure/virtual-machines/windows/sizes)
- [Virtual Machines Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/compute)

To initiate the background task in a separate VM, you can:

- Send a request to an endpoint that the task exposes to run the task on demand directly from your application. The request transfers data that the task requires. The endpoint invokes the task.

- Use a scheduler or timer from your chosen operating system to configure the task to run on a schedule. For example, on Windows, you can use Windows Task Scheduler to run scripts and tasks. If you have SQL Server installed on the VM, use SQL Server Agent to run scripts and tasks.

- Use Logic Apps to initiate the task by adding a message to a queue that the task monitors or by sending a request to an API that the task exposes.

For more information about how you can initiate background tasks, see the previous [Triggers](#triggers) section.

#### Virtual Machines considerations

Consider the following points when you deploy background tasks in an Azure VM:

- Host background tasks in a separate Azure VM to provide flexibility and precise control over initiation, deployment, scheduling, and resource allocation. However, runtime costs increase if you deploy a VM solely for background tasks.

- There's no facility to monitor the tasks in the portal and no automated restart capability for failed tasks. But you can use the [Azure Resource Manager cmdlets](https://devblogs.microsoft.com/scripting/work-with-the-azurerm-cmdlets-part-1) to monitor the status of the VM and manage it. There are no facilities to control processes and threads in compute nodes. Typically, if you use a VM, you have to implement a mechanism that collects data from instrumentation in the task, and also from the operating system in the VM. You can use the [System Center Management Pack for Azure](https://www.microsoft.com/download/details.aspx?id=50013) for this purpose.

- Consider creating monitoring probes that are exposed through HTTP endpoints. You can configure the code for these probes to perform health checks and collect operational information and statistics. You can also use the probes to collate error information and return it to a management application.

For more information, see:

- [Health Endpoint Monitoring pattern](/azure/architecture/patterns/health-endpoint-monitoring)
- [Virtual Machines](https://azure.microsoft.com/services/virtual-machines)
- [Virtual Machines FAQ](/azure/virtual-machines/linux/faq)

### Batch

Consider [Batch](/azure/batch) if you need to run large, parallel high-performance computing (HPC) workloads across tens, hundreds, or thousands of VMs.

Use Batch to prepare the VMs, assign tasks to the VMs, run the tasks, monitor the progress, and automatically scale out the VMs in response to the workload. Batch also provides job scheduling and supports Linux and Windows VMs.

#### Batch considerations

Batch is suitable for intrinsically parallel workloads. You can use Batch to perform parallel calculations with a reduce step at the end, or run [Message Passing Interface (MPI) applications](/azure/batch/batch-mpi) for parallel tasks that require message passing between nodes.

A Batch job runs on a pool of nodes, or VMs. You can allocate a pool only when needed and then delete it after the job finishes. This approach maximizes utilization because nodes aren't idle, but the job must wait for you to allocate nodes. Alternatively, you can create a pool in advance. This approach minimizes the time that it takes for a job to start but can result in nodes that sit idle.

For more information, see:

- [Nodes and pools in Batch](/azure/batch/nodes-and-pools)
- [What is Batch?](/azure/batch/batch-technical-overview)
- [Jobs and tasks in Batch](/azure/batch/jobs-and-tasks)
- [HPC on Azure](/azure/architecture/topics/high-performance-computing#azure-batch)
- [Batch service workflow and resources](/azure/batch/batch-service-workflow-features)

### Azure Kubernetes Service

Use AKS to manage your hosted Kubernetes environment so you can easily deploy and manage containerized applications.

Containers are useful for running background jobs. Some of the benefits include:

- Containers support high-density hosting. You can isolate a background task in a container, while placing multiple containers in each VM.

- Use the container orchestrator to perform internal load balancing, configure the internal network, and perform other configuration tasks.

- You can start and stop containers as needed.

- With Azure Container Registry, you can register your containers inside Azure boundaries, which provides security, privacy, and proximity benefits.

#### AKS considerations

AKS requires an understanding of how to use a container orchestrator.

For more information, see:

- [Overview of containers in Azure](https://azure.microsoft.com/overview/containers)
- [Introduction to container registries in Azure](/azure/container-registry/container-registry-intro)

### Container Apps

With Container Apps, you can build serverless microservices that are based on containers. Container Apps:

- Is optimized for running general purpose containers, especially applications that span many microservices that are deployed in containers.

- Is powered by Kubernetes and open-source technologies, like [Dapr](https://dapr.io), [Kubernetes Event-driven Autoscaling (KEDA)](https://keda.sh/), and [Envoy](https://www.envoyproxy.io).

- Supports Kubernetes-style apps and microservices with features like [service discovery](/azure/container-apps/connect-apps) and [traffic splitting](/azure/container-apps/traffic-splitting).

- Enables event-driven application architectures by supporting scaling that's based on traffic and pulling from event sources like [queues](/azure/container-apps/scale-app), including scale to zero.

- Supports long-running processes and can run [background tasks](/azure/container-apps/background-processing).

#### Container Apps considerations

Container Apps doesn't provide direct access to the underlying Kubernetes APIs. If you require access to the Kubernetes APIs and control plane, use [AKS](/azure/aks/intro-kubernetes). If you want to build Kubernetes-style applications and you don't require direct access to the native Kubernetes APIs and cluster management, use Container Apps for a fully managed experience. Container Apps is best suited for building container microservices.

For more information, see:

- [Container Apps overview](/azure/container-apps/overview)
- [Quickstart: Deploy your first container app](/azure/container-apps/get-started)

## Tradeoffs

- Background jobs introduce more components and dependencies to the system, which can increase the complexity and maintenance costs of the solution. For example, background jobs might require a separate queue service, worker service, monitoring service, and retry mechanism.

- Background jobs can create challenges for data synchronization and process coordination, especially if the background tasks depend on each other or on other data sources. For example, background jobs might handle data consistency problems, race conditions, deadlocks, or timeouts.

- Background jobs might affect the user experience if the results of the background tasks are presented to the user. For example, background jobs might require the user to wait for a notification, refresh the page, or manually check the status of the task. These behaviors can increase the complexity of the user interaction and negatively affect the user experience.

## Related links

- [Compensating Transaction pattern](/azure/architecture/patterns/compensating-transaction)
- [Competing Consumers pattern](/azure/architecture/patterns/competing-consumers)
- [Leader Election pattern](/azure/architecture/patterns/leader-election)
- [Pipes and Filters pattern](/azure/architecture/patterns/pipes-and-filters)
- [Priority Queue pattern](/azure/architecture/patterns/priority-queue)
- [Queue-Based Load Leveling pattern](/azure/architecture/patterns/queue-based-load-leveling)
- [Scheduler Agent Supervisor pattern](/azure/architecture/patterns/scheduler-agent-supervisor)
