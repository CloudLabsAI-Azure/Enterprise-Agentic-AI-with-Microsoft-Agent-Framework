# Exercise 2: Multi-Agent Collaboration and A2A Communication

## Estimated Duration: 90 Minutes

## Overview

In this exercise, you’ll build a multi-agent system using the Microsoft Agent Framework. You’ll define distinct agent roles (Planner, HR, Compliance), deploy them, and configure A2A (Agent-to-Agent) communication to allow one agent to call others. You’ll test a scenario where a user query is delegated through the agent network, and then inspect traces and logs to confirm correct routing.

The Microsoft Agent Framework SDK is the new official development kit for building intelligent, modular agents that can reason, take actions, and collaborate with other agents. It provides:

- Unified Agent Architecture – Replaces AutoGen, Semantic Kernel, and fragmented orchestrators

- Built-in Support for Azure AI Foundry – Deploy agents directly into Foundry’s Agent Service

- Tooling via MCP (Model Context Protocol) – Standardized integration with data, APIs, systems

- Native A2A Communication – Agents can call other agents as autonomous collaborators

This SDK is designed to support enterprise-grade, production-ready agent systems, with reliability, observability, and governance baked in from the start.

## Exercise Objectives

You'll perform the following task in this exercise

- Task 1: Open the Preconfigured VS Code Project
- Task 2: Create Planner Agent
- Task 3: Create HR & Compliance Worker Agents
- Task 4: Define A2A Routing Logic (Agent Graph / Workflow)
- Task 5: Test Multi-Agent Conversation & Inspect Logs

### Task 1: Open the Preconfigured VS Code Project

In this task, you will review the preconfigured folder structure to understand where agent definitions, workflows, and tools are organized. This prepares you to extend the system using the Microsoft Agent Framework SDK.

1. From the LabVM Desktop, open **Visual Studio Code**.

1. Once Visual Studio code is open, click on **file** option and select **open folder** option to open the code file folder.

1. Once in the open folder pane, navigate to `C:\enterprise-agent-code-files` and click on select folder.

1. Please review the folder structure of the enterprise agent.

   ![](/media/ss-27.png)

### Task 2: Create Planner Agent

In this task, you will define the Planner Agent that interprets user queries and decides which specialist agent to delegate tasks to. You will configure the agent using the Agent Framework SDK with role-specific instructions.

### Task 3: Create HR & Compliance Worker Agents

In this task, you will develop domain-specific agents responsible for HR and Compliance knowledge. Each agent will be registered in the Agent Registry to enable discovery and delegation through A2A communication.

### Task 4: Define A2A Routing Logic (Agent Graph / Workflow)

Agent-to-Agent (A2A) is a core capability of the Microsoft Agent Framework that allows one agent to autonomously delegate tasks to another agent.

In this task, you will implement routing logic using an Agent Workflow so the Planner can autonomously call HR or Compliance agents based on query intent. This establishes true multi-agent collaboration.

### Task 5: Test Multi-Agent Conversation & Inspect Logs

In this task, you will run end-to-end test queries through the multi-agent system and observe agent collaboration using logs and telemetry in Azure AI Foundry.

## Summary

In this exercise, you defined three agents (Planner, HR, Compliance) using the Microsoft Agent Framework SDK and registered them. You built a routing workflow to delegate user queries via Agent-to-Agent calls. You tested a multi-agent scenario and inspected logs to confirm correct message routing and execution flow.