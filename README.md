# Microsoft Foundry: Real-Time Stock Analyst with MCP & Web Search tools (Agents v2)

This repo demonstrates the use of a financial analyst agent, built in Microsoft Foundry's new **Agent Service** and powered by **GPT-5.3-Codex**. The solution implements a two-step retrieval process with **Web Search** and the **Model Context Protocol (MCP)** tools to provide the *Codex* model with the latest stock market context.

> [!TIP]
> This solution utilises protected Alpha Vantage MCP service. You can obtain free API key from the vendor site here: https://www.alphavantage.co/support/#api-key.

## 📑 Table of Contents:
- [Part 1: Prerequisites]()
- [Part 2: Environment Setup]()
- [Part 3: Agent Configuration & Tools]()
- [Part 4: Execution Workflow]()
- [Part 5: Testing the Agent]()

## Part 1: Prerequisites
Before running this solution, ensure you have:Azure Subscription with access to Azure AI Foundry.Bing Search resource connected to your Foundry project.Alpha Vantage API Key for the MCP finance service.Azure CLI installed locally for environment configuration.

## Part 2: Environment Setup2.1 Azure AI Foundry SetupCreate an Azure AI Foundry project and deploy the required model (e.g., GPT-5.3-Codex).2.2 Environment VariablesConfigure the following variables to allow the notebook to authenticate and connect to your resources:Environment VariableDescriptionAZURE_FOUNDRY_PROJECT_ENDPOINTYour Azure AI Foundry Project connection string.AZURE_FOUNDRY_CODEX_MODELThe deployment name of your model (e.g., gpt-5.3-codex).ALPHAVANTAGE_API_KEYYour personal API key for stock data retrieval.Part 3: Agent Configuration & Tools3.1 Tool DefinitionsThe agent is equipped with a dual-tooling strategy to ensure data accuracy:WebSearchTool: Used to crawl the live web and identify current market leaders by ticker.MCPTool: Connects to the alpha_finance service to fetch specific fundamentals and news once tickers are identified.Python# Initializing tools within the Agent definition
tools = [
    WebSearchTool(
        user_location = WebSearchApproximateLocation(country = "GB", city = "London")
    ),
    MCPTool(
        server_label = "alpha_finance",
        server_url = f"https://mcp.alphavantage.co/mcp?apikey={ALPHAVANTAGE_API_KEY}",
        require_approval = "never"
    ),
]
Part 4: Execution WorkflowThe agent follows a strict logical sequence to maintain data integrity:Ticker Retrieval: The agent uses Web Search first to identify the top 5 publicly listed companies in a requested sector based on current market capitalization.Context Enrichment: For each identified ticker, the agent calls the MCP tool to retrieve up-to-date fundamentals and recent news.Synthesis: The model processes the combined search and MCP data to rank investments and generate the final output.Part 5: Testing the Agent5.1 Run the AnalysisExecute the provided Jupyter notebook AIFoundry_GPT53Codex_StockDemo.ipynb. The agent will generate a self-contained, dark-theme HTML dashboard directly in the output cell.5.2 HousekeepingThe notebook includes a cleanup step to delete the agent version and close client connections once the analysis is complete to manage project resources efficiently.Python# Clean up resources
project_client.agents.delete_version(agent_name=agent.name, agent_version=agent.version)
project_client.close()
