# Microsoft MCP Server for Enterprise

## Overview

**Microsoft MCP Server for Enterprise** is a programmatic interface that enables AI agents to query enterprise data in your Microsoft Entra tenant using natural language. Built on the [https://modelcontextprotocol\\io/](https://modelcontextprotocol\.io/), an open standard for connecting AI models to external tools and services, it translates natural language requests into Microsoft Graph API calls.

This server empowers developers and IT administrators to integrate natural language querying and management of organizational identity and device data into AI-powered workflows.

## How it works

When an AI client starts, it automatically discovers the Microsoft Graph MCP Server endpoint at mcp.svc.cloud.microsoft/enterprise and retrieves the available tools.

**Example workflow:**

1. **NLP Processing:** The LLM interprets the user's query (e.g., "How many users do we have in our Microsoft Entra tenant?") and determines the intent.

1. **Semantic Search:** The search_for_graph_examples tool finds relevant Graph API queries matching the intent.

1. **Query Selection:** The LLM selects the best-fit API call (e.g., GET /users/\$count).

1. **Execution:** The LLM uses the make_graph_call_readonly tool to execute the API call, respecting user privileges.

1. **API Processing:** The MCP Server forwards the request to Microsoft Graph and returns the results.

1. **Natural Language Response:** The LLM converts the response into a user-friendly answer.

## Tools

The Microsoft MCP Server for Enterprise provides the following tools for AI agents:

- **search_for_graph_examples:** Uses retrieval-augmented generation (RAG) to find Microsoft Graph API calls that match user intent.

- **make_graph_call_readonly:** Executes read-only Microsoft Graph API calls, respecting permissions and throttling.

- **get_graph_entity_properties:** Retrieves properties of specific Microsoft Graph entities to help the AI model understand data structures.

## Usage scenarios

- **IT Helpdesk and Support:** Answer questions like "Which users didn't sign in last month?" or "Is MFA enabled for all administrators?" without needing to know specific Microsoft Graph API endpoints.

- **Administrative Reporting:** Generate reports and insights through conversational queries (for example, "Show me the unassigned licenses in my tenant").

- **API Discovery and Prototyping:** Explore and test Microsoft Graph APIs by using natural language before integrating them into applications.

- **Automation and Scripting:** Integrate with scripts, Microsoft Power Platform (Power Platform) flows, or Microsoft Azure Logic Apps (LA) to enable no-code or low-code solutions for querying Microsoft Entra data.

## Current scope and capabilities

- **Private Preview:** Read-only enterprise IT scenarios focused on Microsoft Entra identity and directory operations (user, group, application, device management, and administrative actions).

- **Security:** All operations respect Microsoft Graph permissions, privileges, and security policies.

## Licensing and costs

- The MCP Server for Enterprise **doesn't require extra cost or separate license**.

- You need the right licenses for the data you access (for example, Microsoft Entra ID P2 for sign-in logs).

- Follows Microsoft Graph API throttling limits.

## Cloud availability

The Microsoft MCP Server for Enterprise is available only in the public cloud (global service).

## Logs

To monitor usage, enable [Microsoft Graph activity logs](../microsoft-graph-activity-logs-overview.md) in your tenant. The system logs all API calls made through the MCP server.

**Filter for MCP Server usage:**

Use the appId (application/client ID) of the MCP server: `e8c77dc2-69b3-43f4-bc51-3213c9d915b4`.

The following sample Kusto query retrieves these logs:

```kusto
MicrosoftGraphActivityLogs
| where TimeGenerated >= ago(30d)
| where AppId == "e8c77dc2-69b3-43f4-bc51-3213c9d915b4"
| project RequestId, TimeGenerated, UserId, RequestMethod, RequestUri, ResponseStatusCode
```

## Next steps

- [Provision MCP](/mcp-server/provision-mcp.md)

## Support and reference

For documentation, troubleshooting, and feedback, refer to the official Microsoft Learn documentation and support channels.

## Security and compliance

All operations respect Microsoft Graph permissions and security policies.

Ensure compliance with your organizational, regulatory, and contractual requirements when integrating the MCP Server.

## No warranty/limitation of liability

This software is provided "as is" without warranties or conditions of any kind, either express or implied. Microsoft isn't liable for any damages that result from use, misuse, or misconfiguration of this software.
