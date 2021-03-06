---
title: "Azure CLI 脚本示例 - 将自定义域映射到 Function App | Microsoft Docs"
description: "Azure CLI 脚本示例 - 将自定义域映射到 Azure 中的 Function App。"
services: functions
documentationcenter: 
author: ggailey777
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: d127e347-7581-47d7-b289-e0f51f2fbfbc
ms.service: functions
ms.workload: na
ms.devlang: azurecli
ms.tgt_pltfrm: na
ms.topic: sample
ms.date: 04/09/2017
ms.author: glenga
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: cd7ab0bbe92fa32d23a841b0b17bee8510f6b406
ms.contentlocale: zh-cn
ms.lasthandoff: 05/15/2017

---
# <a name="map-a-custom-domain-to-a-function-app"></a>将自定义域映射到 Function App

此示例脚本使用相关资源创建 Function App，然后将 `www.<yourdomain>` 映射到它。 若要映射到自定义域，必须在应用服务计划（而不是消耗计划）中创建 Function App。 Azure Functions 仅支持使用 A 记录映射自定义域。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>示例脚本

[!code-azurecli-interactive[主要](../../../cli_scripts/azure-functions/configure-custom-domain/configure-custom-domain.sh?highlight=3 "将自定义域映射到 Function App")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | 创建用于存储所有资源的资源组。 |
| [az storage account create](https://docs.microsoft.com/cli/azure/storage/account#create) | 创建 Function App 所需的存储帐户。 |
| [az appservice plan create](https://docs.microsoft.com/cli/azure/appservice/plan#create) | 创建映射自定义域所需的应用服务计划。 |
| [az functionapp create]() | 创建 Function App。 |
| [az appservice web config hostname add](https://docs.microsoft.com/cli/azure/appservice/web/config/hostname#add) | 将自定义域映射到 Function App。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.microsoft.com/cli/azure/overview)。

可以在 [Azure Functions 文档]()中找到其他 Functions CLI 脚本示例。

