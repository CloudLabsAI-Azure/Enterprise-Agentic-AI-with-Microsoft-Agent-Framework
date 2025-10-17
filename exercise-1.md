# Exercise 1: Advanced Orchestration with Azure AI Foundry and Microsoft Agent Framework

## Estimated Duration: 1 Hour

## Overview

In this exercise, you’ll create your first AI Agent using the Azure AI Foundry portal. You’ll begin by uploading enterprise policy documents and ingesting them into Azure AI Search to prepare a knowledge base. Then, you’ll configure the agent using the Microsoft Agent Framework to enable retrieval-augmented generation (RAG). Finally, you’ll test the agent’s responses and analyze execution logs to observe how it retrieves and processes information.

## Exercise Objectives

You'll perform the following task in this exercise

- Task 1: Prepare Knowledge index
- Task 2: Create an AI Agent in Azure AI Foundry
- Task 3: Connect Azure AI Search for RAG
- Task 4: Test and Observe Agent Execution Logs

### Task 1: Prepare Knowledge index

#### About Knowledge Ingestion and Indexing

Before an AI Agent can answer enterprise questions accurately, it must access trusted data sources. Azure AI Search enables Retrieval-Augmented Generation (RAG) by indexing documents such as policies, contracts, and manuals. An index acts like a searchable catalog that breaks content into chunks, adds metadata, and enables the agent to retrieve the right information during a conversation.

In this task, ingest corporate policy documents into Azure Blob Storage and index them using Azure AI Search to create a searchable knowledge base.

1. As you have logged in to Azure Portal as a pre requisite, from the home page, scroll down, select **Resource Groups** under Navigate.

