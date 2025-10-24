# Exercise 4: Agent Deployment and Runtime Management in Azure AI Foundry

## Estimated Duration: 30 Minutes

## Overview

In this exercise, you’ll deploy your multi-agent system (developed using the Microsoft Agent Framework SDK) into the Azure AI Foundry Agent Service.
You’ll publish the configured agent into a managed runtime environment.

You have built a Chat Response Agent till now, which means:

- It handles single-turn, stateless interactions, responding immediately to user inputs.

- It runs locally within your application or SDK, without a persistent backend.

- Each request is processed independently, with no memory or long-term context retained.

- It is ideal for quick chat experiences or testing core logic before full deployment.

Now, you will update this to be a Persistent Agent in Azure AI Foundry, which means:

- It runs as a managed, long-lived service within the Foundry environment.

- It can maintain state and context across sessions for continuity and learning.

- It supports integration with external tools and other agents using MCP and A2A protocols.

- It is optimized for enterprise-scale reliability, monitoring, and compliance.

## Exercise Objectives

You'll perform the following task in this exercise

- Task 1: Deploy Agents to Azure AI Foundry Agent Service

### Task 1: Deploy Agents to Azure AI Foundry Agent Service

In this task, you’ll update the existing agent to be persistant agent and publish each agent to AI Foundry Agent Service as standalone models.

1.  

   ![](/media/ss-68.png)

## Summary

In this exercise, you successfully deployed your locally built multi-agent system into the Azure AI Foundry Agent Service.
