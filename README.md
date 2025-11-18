# Microsoft MCP Server for Enterprise

## Overview

Built on the open [Model Context Protocol](https://modelcontextprotocol.io), the public preview of **Microsoft MCP Server for Enterprise** lets AI agents access **Microsoft Entra** data by converting natural language queries into Microsoft Graph API calls.
This MCP server empowers developers and IT Administrators to integrate the management of organizational data into AI-powered workflows.

## Quick Start

To get started with the Microsoft MCP Server for Enterprise, follow these steps:

1. Ensure Microsoft Graph PowerShell SDK Modules do not conflict with Microsoft.Entra.Beta module. If you have Microsoft.Graph modules installed, consider uninstalling them first:

   ```powershell
   Install-Module Uninstall-Graph
   Uninstall-Graph -All
   ```

1. Install Microsoft.Entra.Beta PowerShell module (version 1.0.13 or later):

   ```powershell
   Install-Module Microsoft.Entra.Beta -Force -AllowClobber
   ```

1. Connect Microsoft Entra ID to the tenant you'd like to register the MCP Server:

   ```powershell
   Connect-Entra -Scopes 'Application.ReadWrite.All', 'DelegatedPermissionGrant.ReadWrite.All'
   ```

1. Register the MCP Server for Enterprise in your tenant and grant all permissions to Visual Studio Code:

   ```powershell
   Grant-EntraBetaMCPServerPermission -ApplicationName VisualStudioCode
   ```

1. If you have any issue on any of the above steps, please refer to the detailed [installation instructions](https://learn.microsoft.com/powershell/entra-powershell/installation?view=entra-powershell-beta).

1. Click [Install Microsoft MCP Server for Enterprise](https://vscode.dev/redirect/mcp/install?name=Microsoft%20MCP%20Server%20for%20Enterprise&config=%7b%22name%22:%22Microsoft%20MCP%20Server%20for%20Enterprise%22%2c%22type%22:%22http%22%2c%22url%22:%22https://mcp.svc.cloud.microsoft/enterprise%22%7d) to launch VS Code's MCP install page.

1. Click the Install button in VS Code and Login with your Admin account from the tenant above

1. Open Copilot Chat and ask a question about your tenant.

## Tools

This MCP Server is atypical: instead of exposing a separate tool per Microsoft Graph operation, it applies Retrieval-Augmented Generation (RAG) and few-shot prompting to generate complete Microsoft Graph queries.  
It exposes only three tools to implement a reliable and grounded workflow:

1. **`microsoft_graph_suggest_queries`**: Finds relevant Microsoft Graph API calls based on user intent.
1. **`microsoft_graph_get`**: Executes read-only Microsoft Graph API calls, respecting User roles and MCP Client scopes.
1. **`microsoft_graph_list_properties`**: Retrieves properties of specific Microsoft Graph entities to help the AI model

## Current scope and capabilities

For **Public Preview**, our focus is to support **read-only** enterprise IT scenarios focused on Microsoft Entra identity and directory operations (user, group, application, device management, and administrative actions).

In particular, the MCP Server can handle queries related to:

1. **Security posture**: authentication methods/strengths, Conditional Access, Security Defaults.
1. **Privileged access**: Who has which directory roles, how assigned (direct vs group), and PIM status.
1. **Application risk**: Which Apps / Service Principals exist, who owns them, what permissions/SSO they use, and which are ownerless or external.
1. **Access governance**: Who has access to what (users, groups, packages); review decisions, automate joiner/mover/leaver.
1. **Device readiness**: Managed/compliant status, join state, OS/version distribution, and stale or inactive devices.
1. **Provenance and investigation**: End‑to‑end telemetry (sign‑in, audit, provisioning, network), health alerts, and SLA/availability.
1. **Optimize spending & hygiene**: License counts/usage, unused or stale apps/groups, domain configuration and contacts.

## Supported Clients

The Microsoft MCP Server for Enterprise is designed to work with any MCP-compatible client *supporting the latest standard*.

> ⚠️ Notes:
>
> - Dynamic Client Registration (DCR) is not supported, but we are working to support OAuth Client ID Metadata Documents (CIMD) in a future release.
> - ChatGPT is supported only with custom client Id, that is currently available only on the **Pro Plan**.
> - Claude has a bug that prevents authentication with Entra resources, the team is aware and currently working on a fix. It also requires a custom client Id, available only on the **Teams Plan**.

We recommend using it with [Visual Studio Code](https://code.visualstudio.com/) and the [Copilot Chat extension](https://marketplace.visualstudio.com/items?itemName=GitHub.copilot-chat).

## Authorization and permissions

The MCP Server for Enterprise uses Microsoft Graph API to access data in your Microsoft Entra tenant using **delegated permissions** only, and provides a reduced set of permissions exposed by Microsoft Graph.  
Use the following cmdlet to list the permissions provided by the MCP Server for Enterprise:

```powershell
(Get-MgServicePrincipal -Property "Oauth2PermissionScopes" -Filter "AppId eq 'e8c77dc2-69b3-43f4-bc51-3213c9d915b4'").Oauth2PermissionScopes.value | Sort-Object
```

If you'd like to use your own Registered Application, use the following cmdlets to to manage scopes granted to your MCP Client Application:

```powershell
Grant-EntraBetaMCPServerPermission -ApplicationId "<MCP_Client_Application_Id>" -Scopes "<Scope1>", "<Scope2>", "<...>"
Revoke-EntraBetaMCPServerPermission -ApplicationId "<MCP_Client_Application_Id>" -Scopes "<Scope1>", "<Scope2>", "<...>"
```

## Advantages

1. **Remote MCP Server**: Easy to configure, fully compliant, and highly reliable—deployed in the same regions as Microsoft Graph for optimal performance, following the same [Microsoft Graph Throttling limits](https://learn.microsoft.com/graph/throttling-limits#identity-and-access-service-limits).
1. **IT Admins are in control**: MCP clients need specific MCP.* scopes (mirroring Microsoft Graph Scopes) to be granted to access your tenant data.
1. **Simplified architecture**: Works with just 3 tools instead of managing individual tools for every API operation.
1. **High-quality query generation**: Generates accurate queries using over 500 real-world examples through RAG (Retrieval-Augmented Generation).
1. **Full auditability**: Easily audit all MCP operations since they execute under the same App ID with a specific user agent.
1. **No extra license required**: only existing Microsoft Entra and Microsoft Graph API licenses applies.

## Availability, Roadmap and feedback

The Microsoft MCP Server for Enterprise is available only in the public cloud (global service), with support for sovereign clouds planned for a future release.  
We will continue expanding beyond the current Microsoft Entra scenarios, but M365 APIs [will be covered by **Agent 365**](https://learn.microsoft.com/microsoft-agent-365/mcp-servers-overview).  
Support for write operations is planned for a future release.  
Please share suggestions or issues through our feedback form: [Submit feedback](https://forms.office.com/r/Vs1au5MjgH).

## Licensing and costs

- The MCP Server for Enterprise **doesn't require extra cost or separate license**.
- You need the right licenses for the data you access (for example, Microsoft Entra ID Governance or Microsoft Entra ID P2 license for Privileged Identity Management (PIM) data).
- Follows Microsoft Graph API throttling limits.

## Logs

To monitor usage, enable [Microsoft Graph activity logs](../microsoft-graph-activity-logs-overview.md) in your tenant. The system logs all API calls made through the MCP server.

**Filter for MCP Server usage:**

Use the Application (Client ID) of the Microsoft MCP Server for Enterprise: `e8c77dc2-69b3-43f4-bc51-3213c9d915b4`.

The following Kusto query retrieves these logs:

```kusto
MicrosoftGraphActivityLogs
| where TimeGenerated >= ago(30d)
| where AppId == "e8c77dc2-69b3-43f4-bc51-3213c9d915b4"
| project RequestId, TimeGenerated, UserId, RequestMethod, RequestUri, ResponseStatusCode
```

## Support and reference

For documentation, troubleshooting, and feedback, refer to the official Microsoft Learn documentation and support channels.

## Security and compliance

All operations respect Microsoft Graph permissions and security policies.
Ensure compliance with your organizational, regulatory, and contractual requirements when integrating the MCP Server.

## No warranty/limitation of liability

This software is provided "as is" without warranties or conditions of any kind, either express or implied. Microsoft isn't liable for any damages that result from use, misuse, or misconfiguration of this software.
