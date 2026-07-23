---
title: "Blog 3"
date: 2026-07-23
weight: 3
chapter: false
pre: " <b> 3.3. </b> "
---

# AgentCore Harness: From Idea to a Working AI Agent with Just Two APIs

When building an AI agent, calling a language model is usually not the most difficult part.

The complexity lies in building the agent loop, connecting tools, managing memory, maintaining session state, controlling identity, monitoring activity, and deploying a secure execution environment.

AWS created Amazon Bedrock AgentCore Harness to package most of these components as a managed service. Instead of wiring every component together, developers can focus more on business logic and the agent's capabilities.

## Two Main APIs

An agent can be brought into operation through two primary APIs:

- **`CreateHarness`:** Defines the agent.
- **`InvokeHarness`:** Sends requests to and runs the agent.

With `CreateHarness`, developers can configure the model, system prompt, tools, skills, memory, and execution limits. AgentCore manages the orchestration loop behind the scenes.

The application then uses `InvokeHarness` to send a user's request. The agent analyzes the request, selects the appropriate tools, executes them, and returns the result to the application.

## Example: AWS Support Agent

Suppose we want to build an AWS Support Agent that can:

- Read logs from CloudWatch.
- Search AWS documentation.
- Analyze the cause of an error.
- Recommend a solution.
- Create a support case when necessary.

Previously, the development team might have needed to write the agent loop, handle tool calling, manage sessions, build a container, and deploy the runtime.

With AgentCore Harness, we primarily define the model, operating instructions, and tools the agent can use. AWS manages the remaining processing loop.

Each agent session can run in an isolated environment with its own filesystem and shell. This allows the agent to read and write files, run commands, and execute code without sharing an environment with other sessions.

## Connecting Tools Through Configuration

AgentCore Harness can connect to several types of tools:

- **AgentCore Gateway** for accessing APIs and AWS Lambda.
- **Remote MCP servers** for connecting tools that support Model Context Protocol.
- **AgentCore Browser** for browser interaction.
- **AgentCore Code Interpreter** for code execution.
- **Inline Function** for steps that require an external system or human approval.

These tools can be declared through configuration, reducing the amount of glue code developers need to write.

Another notable feature is that the model is separated from the agent's logic. Developers can configure a default model for the Harness and select a suitable model for individual requests instead of tying the entire system to a single model.

## Does AgentCore Harness Replace AgentCore Runtime?

Not entirely.

**AgentCore Harness** is suitable for agents that follow a common pattern: receive a request, reason about it, call tools, and return a result. It favors development speed and avoids the need to build the complete orchestration loop.

**AgentCore Runtime** is more suitable when you need to:

- Choose your own agent framework.
- Build a specialized graph or workflow.
- Control the orchestration process in depth.
- Use custom logic and hooks.
- Deploy an architecture that does not follow a conventional agent loop.

In other words, Harness prioritizes development speed and simplicity, while Runtime prioritizes customization.

## Conclusion

AgentCore Harness does not mean that building an AI agent requires no code. Developers still need to design system prompts, tools, data, IAM roles, access permissions, and mechanisms that control the agent's actions.

However, when orchestration, memory, execution environments, identity, and observability are available as managed capabilities, development teams can spend more time solving the actual business problem.

Instead of building the agent's entire foundation, we can begin with two APIs:

- `CreateHarness` to define the agent.
- `InvokeHarness` to bring the agent into operation.

What do you think: is a managed harness like this best suited for rapid experimentation, or is it already capable enough for production systems?

**Official AWS article:** [Amazon Bedrock AgentCore Harness is now generally available](https://aws.amazon.com/blogs/machine-learning/amazon-bedrock-agentcore-harness-is-now-generally-available-go-from-idea-to-production-grade-agent-in-minutes/)

`#AWS` `#AmazonBedrock` `#AgentCore` `#AgenticAI` `#GenerativeAI`
