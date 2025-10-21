# Exercise 3: Integrating Tools & Data with Model Context Protocol

## Estimated Duration: 90 Minutes

## Overview

In this exercise, you’ll extend your multi-agent system by integrating Model Context Protocol (MCP), a standardized way for agents to access external knowledge and tools.
You’ll connect your agents to an Azure AI Search index, giving them the ability to pull factual information from real enterprise datasets instead of relying solely on general LLM knowledge.

This upgrade transforms your system from “smart agents” to knowledge-grounded agents capable of citing verified company policies and documents.

## Exercise Objectives

You'll perform the following task in this exercise

- Task 1: Build the Azure Search MCP Tool
- Task 2: Attach Tool to Agents, Enrich Prompts, and Run Tests

### Task 1: Build the Azure Search MCP Tool

In this task you will update environment variables with AI Search credentials, create an async tool class that queries Azure AI Search and returns top-N document snippets which agents will use as context.

1. In the Azure Portal, navigate to your resource group, and from the resource list select **ai-knowledge-<inject key="DeploymentID" enableCopy="false"/>** Search service.

   ![](/media/ss-3.png)

1. Select 

   ![](/media/ss-47.png)

1. As you have created a multi-agent system before, in the visual studio code pane, select `.env` file, as you have to add AI Search keys for connection.

1. 