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

1. In visual studio code, before openening new folder, select `.env` file and copy the content and keep it safely in a notepad.

1. Once done, click on **file** option from top menu and select **Open Folder**.

   ![](./media/ss-81.png)

1. In the open folder pane, navigate to `C:\telemetry-codefiles` and click on select folder.

1. Once opened, the files in the explorer menu looks similar to this.

   ![](./media/ss-82.png)

1. Please go through the code files, review how the opentelemetry implemented in all agents and how the tracing is happening.

    >Integration Overview

    >Integrated OpenTelemetry tracing throughout the agent workflow using the agent_framework.observability package.
    >- Imported get_tracer() and used OpenTelemetry spans to capture structured telemetry for each critical operation.
    >- Wrapped key functions (e.g., classification, routing, RAG, ticket creation) in spans with contextual attributes.
    >- Added unified startup observability setup using setup_observability() to configure exporters and metrics pipelines.
    >- Recorded custom attributes such as query text, routing decisions, and fallback methods for deeper visibility.
    >- Enhanced error handling to record exception traces and link each workflow execution to a trace ID for cross-system correlation.

    >File Enhancements

    >main.py – End-to-End Tracing and Metrics
    >- Configured OpenTelemetry tracing pipeline and exporter setup.
    >- Wrapped multi-agent orchestration inside spans for complete workflow visibility.
    >- Added spans for sub-steps: routing, data retrieval (RAG), agent responses, and ticket creation.

    >planner_agent.py – Enhanced Routing Observability
    >- Added a tracer instance (get_tracer()) to monitor classification logic.
    >- Captured raw LLM responses, confidence scores, and fallback keyword metrics as span attributes.
    >- Differentiated between AI-based and heuristic classification with labeled spans (SpanKind.INTERNAL).

    >azure_search_tool.py – RAG Observability
    >- Added spans for Azure Search API calls to measure latency and success rates.
    >- Logged retrieved document counts and payload sizes as custom metrics.
    >- Captured search errors and performance data within OpenTelemetry traces.

    >freshdesk_tool.py – Ticket Creation Observability
    >- Added API call spans to track ticket creation duration and response status.
    >- Logged ticket IDs, tags, and requester details for traceable audit logs.
    >- Monitored external API latency and error responses for better incident tracking.

1. Once reviewed, right-Click on **.env.example (1)** file and select **Rename (2)** to rename the file.

   ![](./media/ss-28.png)

1. Once done, rename the file from **.env.example** --> **.env** to make this environmnet file active for this agent.

   ![](./media/ss-29.png)

1. Now, select the `.env` file and paste the content which you've copied earlier.

1. Once done, add the following app insights variables to the same file.

   ```
   # Observability and Monitoring Configuration
   APPLICATIONINSIGHTS_CONNECTION_STRING=<Instrument-key>
   ENABLE_OTEL=true
   ENABLE_SENSITIVE_DATA=true
   ```

   ![](./media/ss-84.png)

1. Once done, please save the file.

1. Select the **... (1)** option from the top menu to extend the menu. Select **Terminal (2)** and click on **New Terminal (3)**.

    ![](./media/ss-40.png)

1. Run the below command to test out the working of search tool.

    ```
    python main.py
    ```

    ![](./media/ss-80.png)


### Task 2: Visualize Agent Metrics

In this task, you’ll use Azure Application Insights to visualize agent telemetry data. You’ll explore custom metrics for response time, routing accuracy, and ticket creation success. Then, you’ll build interactive Azure Monitor dashboards to display key performance indicators and trends. This helps identify bottlenecks, measure efficiency, and ensure healthy operation of your deployed agents in real time.



## Summary

In this exercise, you configured observability and monitoring for your enterprise agents. Using OpenTelemetry tracing, you captured detailed execution data for every workflow step, and by integrating with Azure Application Insights, you created dashboards to visualize performance metrics and agent health.