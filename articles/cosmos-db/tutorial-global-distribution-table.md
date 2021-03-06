---
title: "表 API 的 Azure Cosmos DB 全局分发教程 | Microsoft Docs"
description: "了解如何使用表 API 设置 Azure Cosmos DB 全局分发。"
services: cosmos-db
keywords: "全局分发, 表"
documentationcenter: 
author: mimig1
manager: jhubbard
editor: cgronlun
ms.assetid: 8b815047-2868-4b10-af1d-40a1af419a70
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: 63c9e530a4982e2e6e478fea56e015fc77851e1d
ms.contentlocale: zh-cn
ms.lasthandoff: 05/31/2017


---
# <a name="how-to-setup-azure-cosmos-db-global-distribution-using-the-table-api"></a>如何使用表 API 设置 Azure Cosmos DB 全局分发

本文介绍如何使用 Azure 门户设置 Azure Cosmos DB 全局分发，然后使用表 API（预览版）进行连接。

本文涵盖以下任务： 

> [!div class="checklist"]
> * 使用 Azure 门户配置全局分发
> * 使用[表 API](table-introduction.md) 配置全局分发

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]


## <a name="connecting-to-a-preferred-region-using-the-table-api"></a>使用表 API 连接到首选区域

为了利用[全局分发](distribute-data-globally.md)，客户端应用程序可以指定要用于执行文档操作的区域优先顺序列表。 对于预览版 Azure 存储 SDK，这可通过在应用配置中设置 `TablePreferredLocations` 配置值完成。 Azure 存储 SDK 会根据 Azure Cosmos DB 帐户配置、当前区域可用性和指定的优先顺序列表，选择最佳的终结点来执行写入和读取操作。

`TablePreferredLocations` 应包含以逗号分隔的首选（多宿主）位置列表，以供读取使用。 每个客户端实例可按首选顺序指定这些区域的一个子集以实现低延迟的读取。 必须使用这些区域的[显示名称](https://msdn.microsoft.com/library/azure/gg441293.aspx)命名这些区域，例如 `West US`。

所有读取请求将发送到 `TablePreferredLocations` 列表中的第一个可用区域。 如果请求失败，客户端会将请求转发到列表中的下一个区域，依此类推。

SDK 只会尝试从 `TablePreferredLocations` 中指定的区域进行读取。 例如，如果数据库帐户在三个区域中可用，但客户端只为 `TablePreferredLocations` 指定了两个非写入区域，那么，即使是在故障转移时，也不会从写入区域为读取提供服务。

SDK 会自动将所有写入请求发送到当前写入区域。

如果未设置 `TablePreferredLocations` 属性，则会从当前写入区域为所有请求提供服务。

```xml
    <appSettings>
      <add key="TablePreferredLocations" value="East US, West US, North Europe"/>           
    </appSettings>
```

本教程到此结束。 阅读 [Azure Cosmos DB 中的一致性级别](consistency-levels.md)，了解如何管理全局复制帐户的一致性。 若要深入了解 Azure Cosmos DB 中全局数据库复制的工作原理，请参阅[使用 Azure Cosmos DB 全局分发数据](distribute-data-globally.md)。

## <a name="next-steps"></a>后续步骤

在本教程中，已完成以下内容：

> [!div class="checklist"]
> * 使用 Azure 门户配置全局分发
> * 使用 DocumentDB API 配置全局分发

现在可以继续学习下一个教程，了解如何使用 Azure Cosmos DB 本地模拟器在本地开发。

> [!div class="nextstepaction"]
> [通过模拟器在本地开发](local-emulator.md)
