# Exercise 3: Integrating Tools & Data with Model Context Protocol

## Estimated Duration: 90 Minutes

## Overview

In this exercise, you’ll extend your enterprise agent system by connecting it to real organizational data and external services using the Model Context Protocol (MCP). You’ll integrate Azure AI Search to provide context-grounded responses from indexed knowledge bases and connect a Freshdesk API to enable agents to perform real-world actions, such as creating HR or Finance tickets.

By completing this exercise, your agents will evolve from static conversational models to intelligent, data-aware, and action-driven assistants capable of securely interacting with enterprise systems.

## Exercise Objectives

You'll perform the following task in this exercise

- Task 1: Build the Azure Search MCP Tool
- Task 2: Attach Tool to Agents, Enrich Prompts, and Run Tests

### Task 1: Build the Azure Search MCP Tool

In this task you will update environment variables with AI Search credentials, create an async tool class that queries Azure AI Search and returns top-N document snippets which agents will use as context.

MCP is a standard that enables AI agents to securely access external knowledge and tools through structured input/output contracts.
It allows agents to retrieve factual information, invoke APIs, and perform actions in a controlled and auditable manner, forming the backbone of grounded and extensible enterprise AI systems.

1. In the Azure Portal, navigate to your resource group, and from the resource list select **ai-knowledge-<inject key="DeploymentID" enableCopy="false"/>** Search service.

   ![](/media/ss-3.png)

1. Select **Keys (1)** from the left menu, under Settings and copy the **Query Key (2)** using the copy option as shown.

   ![](/media/ss-47.png)

1. Once copied, paste it safely in a notepad, select **Indexes** from left menu under Search Management and copy the **Index Name (2)**.

   ![](/media/ss-48.png)

1. As you have created a multi-agent system before, in the visual studio code pane, select `.env` file, as you have to add AI Search keys for connection.

1. In the `.env` file, add this part below the AI Foundry keys.

   ```
   AZURE_SEARCH_ENDPOINT=https://ai-knowledge-<inject key="DeploymentID" enableCopy="false"/>.search.windows.net/
   AZURE_SEARCH_API_KEY=[Query_Key]
   AZURE_SEARCH_INDEX=[Index_Name]
   ```

   ![](/media/ss-49.png)
   
   >**Note:** Please replace the `Query_Key` and `Index_Name` values with the ones you have copied earlier.

1. Once done, click on the **Create Folder** option as shown and when it prompts, provide the folder name as `tools`.

   ![](/media/ss-50.png)

1. Once after creating, the folder structure will look similar to this.

   ![](/media/ss-51.png)

1. Now, select **tools (1)** folder created before and click on **Create file (2)** option as shown. This will create a file under tools folder. 

   ![](/media/ss-53.png)

1. Provide the name for the file as `azure_search_tool.py`. 

1. Once created, add the following code snippet to configure a search tool for your agent.

   ```python
   import os
   import sys
   import aiohttp
   import json
   import asyncio
   from typing import List
   from pathlib import Path

   # Add parent directory to path for imports
   sys.path.append(str(Path(__file__).parent.parent))

   class AzureSearchTool:
      """
      Async MCP-like tool to query Azure Cognitive Search index.
      Reads endpoint/key/index from environment and returns joined content snippets.
      """
      def __init__(self):
         self.endpoint = os.getenv("AZURE_SEARCH_ENDPOINT")
         self.api_key = os.getenv("AZURE_SEARCH_API_KEY")
         self.index_name = os.getenv("AZURE_SEARCH_INDEX")
         if not all([self.endpoint, self.api_key, self.index_name]):
               raise RuntimeError("Azure Search env vars not set (AZURE_SEARCH_ENDPOINT/KEY/INDEX).")
         if not self.endpoint.endswith('/'):
               self.endpoint += '/'
         self.api_version = "2023-11-01"

      async def search(self, query: str, top: int = 5) -> str:
         """
         Query the Azure Search index and return concatenated content snippets.
         """
         url = f"{self.endpoint}indexes/{self.index_name}/docs/search?api-version={self.api_version}"
         headers = {"Content-Type": "application/json", "api-key": self.api_key}
         body = {"search": query, "top": top}

         async with aiohttp.ClientSession() as session:
               async with session.post(url, headers=headers, json=body) as resp:
                  if resp.status != 200:
                     text = await resp.text()
                     raise RuntimeError(f"Azure Search error {resp.status}: {text}")
                  data = await resp.json()
                  docs = data.get("value", [])
                  snippets: List[str] = []
                  for d in docs:
                     snippet = d.get("content") or d.get("text") or d.get("description") or json.dumps(d)
                     snippets.append(snippet.strip())
                  return "\n\n".join(snippets) if snippets else "No results found."

      async def health_check(self):
         """Check if Azure Search service is accessible."""
         try:
               url = f"{self.endpoint}indexes/{self.index_name}?api-version={self.api_version}"
               headers = {"Content-Type": "application/json", "api-key": self.api_key}
               
               async with aiohttp.ClientSession() as session:
                  async with session.get(url, headers=headers) as resp:
                     return {
                           "status": "healthy" if resp.status == 200 else "unhealthy",
                           "status_code": resp.status,
                           "endpoint": self.endpoint,
                           "index": self.index_name
                     }
         except Exception as e:
               return {
                  "status": "error",
                  "error": str(e),
                  "endpoint": self.endpoint,
                  "index": self.index_name
               }

   # Example usage and testing
   async def main():
      """Test the Azure Search tool"""
      try:
         # Load environment variables
         from utils.env import load_env
         load_env()
         
         # Initialize search tool
         search_tool = AzureSearchTool()
         
         # Health check
         health = await search_tool.health_check()
         print(f"Health Status: {json.dumps(health, indent=2)}")
         
         # Test search
         test_queries = [
               "travel reimbursement policy",
               "GDPR compliance requirements", 
               "employee leave policies"
         ]
         
         for query in test_queries:
               print(f"\n{'='*60}")
               print(f"Testing search: {query}")
               print('='*60)
               
               result = await search_tool.search(query, top=3)
               print(result)
               
      except Exception as e:
         print(f"Error testing Azure Search tool: {e}")

   if __name__ == "__main__":
      import asyncio
      asyncio.run(main())
   ```

   ![](/media/ss-54.png)

   >Purpose of AzureSearchTool:
   >- This tool provides an async MCP-compatible interface for agents to query the Azure Cognitive Search index and retrieve factual context snippets relevant to user queries.

   >Environment-Based Configuration:
   >- The tool reads Azure Search credentials — endpoint, API key, and index name — directly from environment variables, ensuring secure and flexible configuration.

   >Core Search Functionality (search method):
   >- The search() method sends an async POST request to the Azure Search REST API, fetching top-matching documents and concatenating their content fields into a single contextual string.

   >Testing and Diagnostic Mode:
   >- The file includes a built-in main() routine that loads environment variables, performs a live health check, and runs sample queries for quick standalone validation before agent integration.

1. Once done, please save the file. Click on the **file** option from top menu, select **save** to save the file.

   ![](/media/ss-39.png)

1. Select the **... (1)** option from the top menu to extend the menu. Select **Terminal (2)** and click on **New Terminal (3)**.

   ![](/media/ss-40.png)

1. Run the below command to test out the working of search tool.

   ```
   python .\tools\azure_search_tool.py
   ```

   ![](/media/ss-57.png)

1. You have successfully created an MCP tool that connects your Azure AI Search data to the agent, enabling it to retrieve relevant context from your indexed knowledge base.

### Task 2: Attach Tool to Agents, Enrich Prompts, and Run Tests

In this task you will attach the AzureSearchTool to HR/Finance/Compliance agents, update orchestration to fetch context before calling agents, and run batch & interactive tests.

1. As you have created the tool, now you have to change the orchestration of the agent to trigger the search tool before giving a response.

1. In the Visual Studio Code explorer, open the `main.py` file and replace its existing content with the code provided below.

   ```python
   import asyncio
   import time
   import logging
   import re
   from typing import Dict, Any
   from utils.env import load_env
   from agents.planner_agent import build_planner_agent, classify_target
   from agents.hr_agent import build_hr_agent
   from agents.compliance_agent import build_compliance_agent
   from agents.finance_agent import build_finance_agent
   from tools.azure_search_tool import AzureSearchTool

   # Configure logging
   logging.basicConfig(level=logging.INFO, format='%(asctime)s - %(levelname)s - %(message)s')

   async def run_multi_agent(query: str, agents: Dict[str, Any]) -> Dict[str, Any]:
      """
      Advanced multi-agent system with routing, search context, and ticket creation capabilities.
      """
      start_time = time.time()
      
      try:
         # Step 1: Route the query
         logging.info(f"Routing query: {query[:50]}...")
         target = await classify_target(agents["planner"], query)
         logging.info(f"Query routed to: {target}")
         
         # Step 2: Retrieve relevant context using Azure Search
         logging.info("Retrieving context from knowledge base...")
         context = await agents["search_tool"].search(query, top=3)
         
         # Step 3: Create enriched prompt with context
         enriched_prompt = f"""
   Context from Knowledge Base:
   {context}

   ---

   User Question: {query}

   Please provide a comprehensive answer based PRIMARILY on the context information provided above. 
   Use the knowledge base content as your primary source of truth. If the context contains relevant 
   information, base your answer on that. Only supplement with general knowledge if the context 
   doesn't cover the specific question.

   If no relevant information is found in the context, clearly state that and provide general guidance 
   while recommending the user contact the appropriate department for specific details.
   """
         
         # Step 4: Get response from appropriate agent
         agent_mapping = {
               "HR": ("hr", "HRAgent"),
               "FINANCE": ("finance", "FinanceAgent"), 
               "COMPLIANCE": ("compliance", "ComplianceAgent")
         }
         
         if target in agent_mapping:
               agent_key, agent_name = agent_mapping[target]
               answer = await agents[agent_key].run(enriched_prompt)
         else:
               # Fallback to HR if routing unclear
               logging.warning(f"Unknown target '{target}', falling back to HR")
               answer = await agents["hr"].run(enriched_prompt)
               target = "HR"
               agent_name = "HRAgent"
         
         answer_text = str(answer)
         
         # Step 6: Process response
         response_time = time.time() - start_time
         
         return {
               "query": query,
               "routed_to": target,
               "agent_name": agent_name,
               "answer": answer_text,
               "context_retrieved": len(context) > 100,  # Simple check if context was found
               "ticket_created": False,  # Tickets only created in interactive mode
               "ticket_info": None,
               "response_time": round(response_time, 2),
               "timestamp": time.strftime("%Y-%m-%d %H:%M:%S"),
               "success": True
         }
         
      except Exception as e:
         logging.error(f"Error processing query: {e}")
         return {
               "query": query,
               "routed_to": "ERROR",
               "agent_name": "ErrorHandler",
               "answer": f"I apologize, but I encountered an error processing your request: {str(e)}",
               "context_retrieved": False,
               "ticket_created": False,
               "ticket_info": None,
               "response_time": round(time.time() - start_time, 2),
               "timestamp": time.strftime("%Y-%m-%d %H:%M:%S"),
               "success": False
         }

   def format_response(result: Dict[str, Any]) -> str:
      """Format the agent response for display."""
      status_icon = "✅" if result["success"] else "❌"
      context_icon = "📚" if result.get("context_retrieved") else "📭"
      ticket_icon = "🎫" if result.get("ticket_created") else ""
      
      formatted = f"""
   {status_icon} Agent Response Summary:
   ┌─ Routed to: {result['routed_to']} ({result['agent_name']})
   ├─ Response time: {result['response_time']}s
   ├─ Context retrieved: {context_icon} {'Yes' if result.get('context_retrieved') else 'No'}
   ├─ Ticket created: {ticket_icon} {'Yes' if result.get('ticket_created') else 'No'}
   ├─ Timestamp: {result['timestamp']}
   └─ Status: {'Success' if result['success'] else 'Error'}

   💬 Answer:
   {result['answer']}
   """
      
      # Add ticket details if available
      if result.get("ticket_info") and result["ticket_info"].get("success"):
         ticket = result["ticket_info"]["ticket"]
         formatted += f"""

   🎫 Ticket Details:
   ├─ ID: #{ticket['id']}
   ├─ Status: {ticket['status']}
   ├─ Priority: {ticket['priority']}
   └─ URL: {ticket['url']}
   """
      
      return formatted

   async def run_interactive_mode(agents: Dict[str, Any]):
      """Interactive mode for real-time queries."""
      print("\n🤖 Enterprise Agent System - Interactive Mode")
      print("Available agents: HR, Finance, Compliance")
      print("Type 'quit' to exit, 'help' for commands\n")
      
      while True:
         try:
               query = input("Enter your question: ").strip()
               
               if query.lower() in ['quit', 'exit', 'q']:
                  print("👋 Goodbye!")
                  break
               elif query.lower() == 'help':
                  print("""
   📋 Available Commands:
   - Ask any question about HR, Finance, or Compliance
   - 'quit' or 'exit' - Exit the system
   - 'help' - Show this help message

   🎯 Example questions:
   - "What's the travel reimbursement limit for meals?"
   - "How many vacation days do employees get?"  
   - "Do we need GDPR compliance for EU customers?"
   """)
                  continue
               elif not query:
                  continue
                  
               result = await run_multi_agent(query, agents)
               print(format_response(result))
               
         except KeyboardInterrupt:
               print("\n👋 Goodbye!")
               break
         except Exception as e:
               logging.error(f"Interactive mode error: {e}")
               print(f"❌ Error: {e}")

   async def run_batch_tests(agents: Dict[str, Any]):
      """Run focused test queries with grounded data integration."""
      test_queries = [
         "What is the travel reimbursement limit for hotel stays?",
         "How many vacation days are allowed per year?",
         "Do we need GDPR compliance for EU customers?",
         "What's the budget approval process for large purchases?"
      ]
      
      print("🧪 Running focused batch tests with grounded data integration...\n")
      
      for i, query in enumerate(test_queries, 1):
         print(f"{'='*80}")
         print(f"TEST {i}/{len(test_queries)}: {query}")
         print(f"{'='*80}")
         
         result = await run_multi_agent(query, agents)
         print(format_response(result))
         
         # Small delay between queries for better readability
         if i < len(test_queries):
               await asyncio.sleep(1.0)  # Longer delay for tool operations

   async def main():
      """Main application entry point with enhanced features and tool integration."""
      print("🚀 Initializing Enterprise Agent System with Tools...")
      
      try:
         # Load environment and build agents
         load_env()
         logging.info("Building agent network...")
         
         # Build core agents
         agents = {
               "planner": await build_planner_agent(),
               "hr": await build_hr_agent(), 
               "compliance": await build_compliance_agent(),
               "finance": await build_finance_agent()
         }
         
         # Initialize and attach tools
         logging.info("Initializing tools...")
         
         try:
               search_tool = AzureSearchTool()
               agents["search_tool"] = search_tool
               
               # Test search tool
               health = await search_tool.health_check()
               if health["status"] == "healthy":
                  logging.info("✅ Azure Search tool initialized successfully")
               else:
                  logging.warning(f"⚠️ Azure Search tool health check failed: {health}")
                  
         except Exception as e:
               logging.error(f"Failed to initialize Azure Search tool: {e}")
               # Create mock search tool for testing
               class MockSearchTool:
                  async def search(self, query, top=3):
                     return f"📭 Mock search results for: {query}\n(Azure Search tool not configured)"
               agents["search_tool"] = MockSearchTool()
         
         # Freshdesk integration removed - focusing on grounded search responses only
         
         logging.info("✅ All agents and tools initialized")
         
         # Check if running interactively or in batch mode
         import sys
         if len(sys.argv) > 1 and sys.argv[1] == "--interactive":
               await run_interactive_mode(agents)
         else:
               await run_batch_tests(agents)
               
      except Exception as e:
         logging.error(f"System initialization failed: {e}")
         print(f"❌ Failed to start system: {e}")
         
         # Try to run with minimal configuration
         logging.info("Attempting to run with minimal configuration...")
         try:
               minimal_agents = {
                  "planner": await build_planner_agent(),
                  "hr": await build_hr_agent(),
                  "compliance": await build_compliance_agent(), 
                  "finance": await build_finance_agent(),
                  "search_tool": type('MockSearch', (), {'search': lambda self, q, top=3: f"Mock search for: {q}"})()
               }
               await run_batch_tests(minimal_agents)
         except Exception as minimal_error:
               print(f"❌ Even minimal configuration failed: {minimal_error}")

   if __name__ == "__main__":
      asyncio.run(main())
    ```

   ![](/media/ss-56.png)

   >Purpose of Updated Main Script:
   >- This version extends the earlier orchestrator to integrate Azure AI Search via MCP, enabling each agent’s responses to be context-grounded using enterprise data instead of generic LLM reasoning.

   >Search Tool Integration (New Feature):
   >- The AzureSearchTool instance is initialized and attached to the agents dictionary as agents["search_tool"].
   >- Before the system runs, it performs a health check to confirm Azure AI Search connectivity and index readiness.

   >Context Enrichment Logic (Enhanced run_multi_agent):
   >- For every query, the system now fetches related text snippets from Azure Search (context = await agents["search_tool"].search(query)).
   >- The returned context is embedded directly into the prompt before sending to the specialized agent.
   >- The LLM is explicitly instructed to base answers primarily on this contextual data, ensuring factually grounded responses.

1. Once done, please save the file. Click on the **file** option from top menu, select **save** to save the file.

   ![](/media/ss-39.png)

1. Select the **... (1)** option from the top menu to extend the menu. Select **Terminal (2)** and click on **New Terminal (3)**.

   ![](/media/ss-40.png)

1. Once the terminal is open, run the following command to run the agent and review the responses for the test prompts provided in the code file.

   ```
   python main.py
   ```

   ![](/media/ss-55.png)

   > Check **Context retrived** parameter and review how the agent is getting the context from the grounded data.

1. Now, run the agent again on interactive mode by adding `--interactive` flag. This lets you input the question and get the response back. Provide the below prompt as question once it asks.

   ```
   Is employee data protected under GDPR?
   ```
   
1. Once after getting the response, in the next prompt add `q` to quit the agent or stop the agent.

   ![](/media/ss-43.png)

### Task 3: Setting up Freshworks for Ticket Management

In this task, you will set up and configure Freshworks to enable tickets management and a enterprise integration for your multi-agent system.

**Freshworks** is a cloud-based customer service and engagement platform designed to improve customer support operations and enhance user satisfaction. It offers a suite of tools for ticket management, live chat, help center creation, and customer self-service. Freshworks supports omnichannel communication, enabling businesses to manage customer interactions across email, chat, phone, and social media from a centralized interface. Its automation features help streamline workflows, assign tickets, and provide analytics for performance tracking. Now you will set up the Freshworks account.

1. Navigate to [Freshworks Portal](https://www.freshworks.com/freshdesk/lp/home/?tactic_id=3387224&utm_source=google-adwords&utm_medium=FD-Search-Brand-India&utm_campaign=FD-Search-Brand-India&utm_term=freshdesk&device=c&matchtype=e&network=g&gclid=EAIaIQobChMIuOK90qvLjQMV_dQWBR3JAi9VEAAYASAAEgK87_D_BwE&audience=kwd-30002131023&ad_id=282519464145&gad_source=1&gad_campaignid=671502402) using a new tab in your browser.

   >Note: Since you are working within a VM, please copy the above link and open it in the browser inside the VM.

1. In the portal, select **Start free trial** to start the free trial.

   ![](./media/fw1.png)

1. In the next pane, provide these details and click on **Try it free (5)**:

   - **First name:** `ODL` **(1)**
   - **Last name:** `User` **(2)**
   - **Work email:** **<inject key="AzureAdUserEmail"></inject>** **(3)**
   - **Company name:** `Contoso` **(4)**

   ![](./media/fw2.png)

1. In the next pane, provide these details and click on **Next (4)**:

   - **What industry are you from ?:** from the list, select **Software and internet (1)**
   - **How many employees are there in your company?:** select **1-10 (2)**
   - select **I'm trying customer service software for the first time (3)**

   ![](./media/fw3.png)

1. Once done, navigate to [Outlook](https://go.microsoft.com/fwlink/p/?LinkID=2125442&clcid=0x409&culture=en-us&country=us).

1. In the pick an account pane, select the account which you are assigned for this lab.

   ![](./media/fw4.png)

1. In the freshworks verification email, open and click on **Activate Account**.

   ![](./media/fw5.png)

   >**Note:** If you're unable to locate the activation email from Freshworks, please wait a few minutes, as there might be a delay in email delivery. If the email doesn't arrive after some time, consider reinitiating the steps to activate your free trial in a new private/incognito window. Additionally, check your spam or junk folder, as the email might have been filtered there.

1. In the next pane, provide **<inject key="AzureAdUserPassword"></inject>** as **Enter password (1)** and provide the same password for **Confirm password (2)**. Click on **Activate your account (3)**.

   ![](./media/fw6.png)

1. Once you are in the portal, click on the **Profile (1)** icon from top right corner and select **Profile settings (2)**.

   ![](./media/fw7.png)

1. In the profile page, click on **View API Key** to get the API Keys.

   ![](./media/fw8.png)

   >**Note:** If you are not able to find this option, please minimize the screensize using **CTRL + -**.

1. In the next pane, complete the **CAPTCHA**.

   ![](./media/fw9.png)

1. Please copy the API Key to a notepad, you will be using this further.

   ![](./media/fw10.png)

1. From the browser tab, please copy the **Account URL** as shown and copy the value to notepad. You will be using this further.

   ![](./media/ss-67.png)

1. From the left, click on **Tickets** icon from left menu, you can see some default tickets which are present.

   ![](./media/fw11.png)

1. Once done, navigate to Visual Studio Code pane and open `.env` file.

1. In the `.env` file add the following content and add the key and domain URL that you copied earlier.

   ```
   # Freshdesk Configuration
   FRESHDESK_DOMAIN=[Domain_URL]
   FRESHDESK_API_KEY=[API_Key]
   ```

   ![](/media/ss-58.png)

### Task 4: Connect Agents to an External API (Freshdesk MCP Integration)

In this task, you’ll create an additional MCP tool that connects your agents to an external Freshdesk instance via its REST API.
This tool will allow agents, especially Finance and HR to create real tickets when users request actions such as reimbursements, travel approvals, or policy clarification

## Summary

In this exercise, you successfully tested the complete multi-agent system where agents retrieved relevant information from Azure AI Search, identified the appropriate department to handle each query, and created Freshdesk tickets when necessary. This demonstrated how the agents can combine contextual reasoning with real-world actions, making them more efficient and practical for enterprise workflows.



  