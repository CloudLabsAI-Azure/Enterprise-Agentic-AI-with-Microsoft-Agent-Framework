# Exercise 5: AgentOps - Observability, Governance and Lifecycle Management 

## Estimated Duration: 30 Minutes

## Overview

In this exercise, you will focus on AgentOps — the discipline of monitoring, governing, and managing AI agents in production environments.
You’ll explore how to enable observability and telemetry using the Microsoft Agent Framework’s built-in integration with Application Insights using **OpenTelemetry**.

#### About OpenTelemetry in Microsoft Agent Framework

The Microsoft Agent Framework natively integrates with OpenTelemetry, the open standard for distributed tracing, metrics, and logging. It provides end-to-end visibility into agent behavior by automatically capturing telemetry data such as span traces, tool calls, model responses, and workflow performance. Using this integration, developers can export observability data directly to Azure Monitor, Application Insights, or any other OpenTelemetry-compatible backend. This standardized approach helps track every agent action across complex multi-agent systems, enabling performance tuning, troubleshooting, and compliance auditing with minimal configuration.

## Exercise Objectives

You'll perform the following task in this exercise

- Task 1: Enable Observability of Agent with OpenTelemetry
- Task 2: Visualize Agent Metrics

### Task 1: Enable Observability of Agent with OpenTelemetry

In this task, you’ll integrate OpenTelemetry and Agent Framework observability into your project. You’ll configure telemetry exporters, initialize tracing with `setup_observability()`, and capture detailed spans for each stage of your workflow, including agent routing, Azure AI Search retrieval, and ticket creation. This enables unified visibility into agent behavior and cross-system correlation using trace IDs in Application Insights.

1. Instead of modifying the previous code again, you’ll work in a new folder that already contains the updated observability-enabled files. Understand how telemetry, tracing, and monitoring are integrated using Microsoft Agent Framework Observability and Application Insights. 

1. In visual studio code, click on **file** option from top menu and select **Open Folder**.

   ![](/media/ss-80.png)

### Task 2: Visualize Agent Metrics

In this task, you’ll use Azure Application Insights to visualize agent telemetry data. You’ll explore custom metrics for response time, routing accuracy, and ticket creation success. Then, you’ll build interactive Azure Monitor dashboards to display key performance indicators and trends. This helps identify bottlenecks, measure efficiency, and ensure healthy operation of your deployed agents in real time.

## Summary

In this exercise, you configured observability and monitoring for your enterprise agents. Using OpenTelemetry tracing, you captured detailed execution data for every workflow step, and by integrating with Azure Application Insights, you created dashboards to visualize performance metrics and agent health.