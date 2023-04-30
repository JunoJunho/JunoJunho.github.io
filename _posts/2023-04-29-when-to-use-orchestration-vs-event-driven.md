---
title: When to use ? Orchestration vs Event Driven Architecture
excerpt: Comes from my experience ?

toc: true
toc_sticky: true
tags: [Software Engineering, AWS, Architecture]
categories:
  - Software Engineering
last_modified_at: 2023-04-29T17:22:00
---

![architecture_img](../../img/post/230429/pexels-yentl-jacobs-157811.jpg)

This year, I am a position driving medium size product to satisfy this year’s customer requests. During the design phase, I realized that I need one technical architecture to connect multiple components and let them work closely together.

Two popular patterns have emerged to tackle the challenges of my use cases: orchestration and event-driven architecture. While they share some similarities, understanding their differences is crucial to choosing the right approach for my project.

Orchestration ?
----
Orchestration is a pattern that revolves around a central controller, known as an orchestrator, which coordinates and manages the execution of various tasks or services in a predefined sequence. Let's consider an example in the context of AWS services: AWS Step Functions.

[AWS Step Functions](https://aws.amazon.com/step-functions/#:~:text=AWS%20Step%20Functions%20is%20a,machine%20learning%20(ML)%20pipelines.) is a fully managed service that allows you to coordinate multiple AWS services into serverless workflows. With Step Functions, you can define the sequence of tasks, dependencies, and conditions using a JSON-based language called Amazon State Language (ASL). The orchestrator in this case is Step Functions itself, which takes care of invoking the individual tasks or services, tracking their execution status, and controlling the overall workflow.

For instance, imagine a workflow that involves processing an image uploaded to an Amazon S3 bucket. You can use AWS Step Functions to orchestrate the following steps: triggering the image processing Lambda function, waiting for its completion, and then sending a notification through Amazon SNS. Step Functions provides fault tolerance, retries, and error handling capabilities, making it an excellent choice for orchestrating complex workflows within the AWS ecosystem.

Event Driven Architecture ?
----
Event-driven architecture (EDA) revolves around the flow of events and the reactions or processing that occurs in response to those events. In an event-driven system, components or services communicate by producing and consuming events. AWS provides several services that support event-driven architectures, with AWS Lambda and Amazon EventBridge being prominent examples.

AWS Lambda is a serverless compute service that allows you to run your code in response to events. It can be triggered by a variety of event sources, such as changes in an Amazon S3 bucket, updates in a DynamoDB table, or even custom events published through Amazon EventBridge. Lambda functions encapsulate the business logic and are executed in a highly scalable and managed environment.

For instance, consider a scenario where you have an application that needs to react to user sign-ups. By utilizing AWS Lambda and Amazon Cognito, you can configure a Lambda function to be triggered whenever a user signs up through the Cognito user pool. This function could perform various actions, such as sending a welcome email, storing user details in a database, or invoking other services based on the event data. The event-driven architecture enables loose coupling, scalability, and extensibility, allowing you to add new reactions to events without modifying existing components.

Amazon EventBridge is another powerful AWS service that simplifies the building of event-driven architectures. It acts as a central event bus, receiving events from various sources and routing them to specific targets. With EventBridge, you can define rules that match incoming events based on their content and direct them to specific Lambda functions, Step Functions, or other AWS services.

What did I choose ?
-----
So, this is my conclusion. I reviewed deeply and explored existing solutions that other engineers already considered. Obviously, there is less coupling in event driven architecture than orchestration based architecture. There is a lack of tracability in event driven architecture. However, it gives strength on isolating the failures with less coupling. It means I can replace any component easily by confirming interface or event consumption mechanism. Furthermore, event driven architecture seems more cool than orchestration and uses more fancy ones.

At the end of the day, I discussed with team and my senior engineer. We realized that using fancy stuffs are good, but the thing is that we need to use less resources but gaining more values from what we want to build. So, I chose to use orchestration based flow and improve from existing product slowly. Yes, it is not cool, but this is the best way to satisfy most of my stakeholders. If you are at the beginning phase of the design, I hope this article helps your decision.