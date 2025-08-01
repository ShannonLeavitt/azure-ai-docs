---
title: "Quickstart: Analyze image content"
titleSuffix: Azure AI services
description: Get started using Azure AI Content Safety to analyze image content for objectionable material.
author: PatrickFarley
manager: nitinme
ms.service: azure-ai-content-safety
ms.custom: build-2023, devx-track-python, devx-track-dotnet, devx-track-extended-java, devx-track-js
ms.topic: quickstart
ms.date: 07/14/2025
ms.author: pafarley
zone_pivot_groups: programming-languages-content-safety-2
---

# QuickStart: Analyze image content

Get started with the Content Studio, REST API, or client SDKs to do basic image moderation. The Azure AI Content Safety service provides you with AI algorithms for flagging objectionable content. Follow these steps to try it out.

For more information on image moderation, see the [Harm categories concept page](./concepts/harm-categories.md). For API input limits, see the [Input requirements](./overview.md#input-requirements) section of the Overview. 

> [!CAUTION]
> 
> The sample data and code may contain offensive content. User discretion is advised.

::: zone pivot="programming-language-foundry-portal"

[!INCLUDE [Foundry portal quickstart](./includes/quickstarts/foundry-quickstart-image.md)]

::: zone-end

::: zone pivot="programming-language-rest"

[!INCLUDE [REST API quickstart](./includes/quickstarts/rest-quickstart-image.md)]

::: zone-end

::: zone pivot="programming-language-csharp"

[!INCLUDE [.NET SDK quickstart](./includes/quickstarts/csharp-quickstart-image.md)]

::: zone-end

::: zone pivot="programming-language-python"

[!INCLUDE [Python SDK quickstart](./includes/quickstarts/python-quickstart-image.md)]

::: zone-end

::: zone pivot="programming-language-java"

[!INCLUDE [Python SDK quickstart](./includes/quickstarts/java-quickstart-image.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

[!INCLUDE [Python SDK quickstart](./includes/quickstarts/javascript-quickstart-image.md)]

::: zone-end

::: zone pivot="programming-language-typescript"

[!INCLUDE [TypeScript SDK quickstart](./includes/quickstarts/typescript-quickstart-image.md)]

::: zone-end

## Clean up resources

If you want to clean up and remove an Azure AI services subscription, you can delete the resource or resource group. Deleting the resource group also deletes any other resources associated with it.

- [Azure portal](../multi-service-resource.md?pivots=azportal#clean-up-resources)
- [Azure CLI](../multi-service-resource.md?pivots=azcli#clean-up-resources)

## Related content

* [Harm categories](./concepts/harm-categories.md)
* Configure filters for each category and test on datasets using [Content Safety Studio](studio-quickstart.md), export the code and deploy.

