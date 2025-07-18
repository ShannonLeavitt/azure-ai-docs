---
title: 'How to use Microsoft SharePoint content with Azure AI Agent Service'
titleSuffix: Azure OpenAI
description: Learn how to ground Azure AI Agents using Microsoft SharePoint content.
services: cognitive-services
manager: nitinme
ms.service: azure-ai-agent-service
ms.topic: how-to
ms.date: 06/05/2025
author: aahill
ms.author: aahi
ms.custom: azure-ai-agents
---
# Use the Microsoft SharePoint tool (preview)

Integrate your agents with the **Microsoft SharePoint** to chat with your private documents securely. You can connect to your SharePoint site, such as `contoso.sharepoint.com/sites/policies` to ground your Agents with that data. When a user sends a query, the agent will determine if SharePoint should be leveraged or not. If so, it will send a query using the SharePoint tool, which checks if the user has a Microsoft 365 Copilot license and use managed identity to retrieve relevant documents they have access to. The scope of retrieval includes all supported documents in this SharePoint site. Lastly, the agent will generate responses based on retrieved information. With identity passthrough (On-Behalf-Of) authorization, this integration simplifies access to enterprise data in SharePoint while maintaining robust security, ensuring proper access control and enterprise-grade protection. 

## Usage support

|Azure AI foundry support  | Python SDK |	C# SDK | JavaScript SDK | REST API |Basic agent setup | Standard agent setup |
|---------|---------|---------|---------|---------|---------|---------|
| ✔️ | - | - | - | ✔️ | ✔️ | ✔️ |

## Prerequisites

* Developers and end users have Microsoft 365 Copilot license.
* Developers and end users have at least `Azure AI User` RBAC role. 
* Developers and end users have at least `READ` access to the SharePoint site.

## Setup  

> [!NOTE]
> * Supported document types: text data in the following format: `.pdf`, `.docx`, `.ppt`, `.txt`, `.aspx` 
> * We recommend you start with SharePoint sites that have: a simple folder structure and a small number of short documents. 

1. Create an agent by following the steps in the [quickstart](../../quickstart.md).

1. You can add the SharePoint tool to an agent programatically using the code examples listed at the top of this article, or the Azure AI Foundry portal. If you want to use the portal, in either the **Create and debug** or **Agent playground** screen for your agent, scroll down the setup pane on the right to knowledge. Then select **Add**.

   :::image type="content" source="../../media\tools\knowledge-tools.png" alt-text="A screenshot showing the available tool categories in the Azure AI Foundry portal." lightbox="../../media\tools\knowledge-tools.png":::

1. Select **SharePoint** and follow the prompts to add the tool. You can only add one per agent.

1. Click to add a new connection. Once you have added a connection, you can directly select from existing list.
   1. To create a new connection, you need to find `site_url` in your SharePoint site. You can add either a SharePoint site or a SharePoint folder. For a SharePoint site, it will look like `https://microsoft.sharepoint.com/teams/<site_name>`. For a SharePoint folder, it will look like `https://microsoft.sharepoint.com/teams/<site_name>/Shared%20documents/<folder_name>`

   1. Then, you can add it to your connection. Make sure you have selected the **is secret** option.

## Next steps

* [How to use the SharePoint tool](./sharepoint-samples.md)
* Reference articles for content retrieval used by the tool:
    * [Overview of the Microsoft 365 Copilot Retrieval API](/microsoft-365-copilot/extensibility/api-reference/retrieval-api-overview).
    * [Semantic indexing for Microsoft 365 Copilot](/microsoftsearch/semantic-index-for-copilot)
