---
title: "Azure 搜索中的索引器 | Microsoft Docs"
description: "对 Azure SQL 数据库、Azure Cosmos DB 或 Azure 存储爬网，提取可搜索的数据并填充 Azure 搜索索引。"
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
tags: azure-portal
ms.assetid: 34a7694c-8fd9-46b1-8900-cefdd7236323
ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 05/01/2017
ms.author: heidist
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 52b154895fca9fc465a9c6cc2fb6bf2d5384b057
ms.contentlocale: zh-cn
ms.lasthandoff: 05/10/2017

---

# <a name="indexers-in-azure-search"></a>Azure 搜索中的索引器
> [!div class="op_single_selector"]
>
> * [概述](search-indexer-overview.md)
> * [门户](search-import-data-portal.md)
> * [Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
> * [Azure Cosmos DB](search-howto-index-documentdb.md)
> * [Azure Blob 存储](search-howto-indexing-azure-blob-storage.md)
> * [Azure 表存储](search-howto-indexing-azure-tables.md)
>
>

Azure 搜索中的 **索引器** 是一种爬网程序，它从外部数据源提取可搜索的数据和元数据，并根据索引与数据源之间字段到字段的映射填充索引。 因为不需要编写任何将数据推送到索引的代码，该服务就能拉取数据，因此这种方法有时也称为“拉取模式”。

可以单独使用索引器来引入数据，也可以结合索引器使用多种技术来加载索引中的部分字段。

可以按需运行索引器，也可以采用每 15 分钟运行一次的定期数据刷新计划来运行索引器。 要进行更频繁的更新，则需要采用“推送模式”，便于同时更新 Azure 搜索和外部数据源中的数据。

## <a name="approaches-for-creating-and-managing-indexers"></a>创建及管理索引器的方法
对于普遍可用的索引器（如 Azure SQL 或 Azure Cosmos DB），可以采用以下方法来创建和管理索引器：

* [门户 > 导入数据向导](search-get-started-portal.md)
* [服务 REST API](https://msdn.microsoft.com/library/azure/dn946891.aspx)
* [.NET SDK](https://msdn.microsoft.com/library/azure/microsoft.azure.search.iindexersoperations.aspx)

## <a name="basic-configuration-steps"></a>基本配置步骤
索引器可提供数据源独有的功能。 因此，索引器或数据源配置的某些方面会因索引器类型而不同。 但是，所有索引器的基本构成元素和要求都相同。 下面介绍所有索引器都适用的共同步骤。

### <a name="step-1-create-an-index"></a>步骤 1：创建索引
索引器会自动执行某些与数据引入相关的任务，但不会自动创建索引。 先决条件是必须具有预定义的索引，且索引的字段必须与外部数据源中的字段匹配。 有关构建索引的详细信息，请参阅 [创建索引（Azure 搜索 REST API）](https://msdn.microsoft.com/library/azure/dn798941.aspx)。

### <a name="step-2-create-a-data-source"></a>步骤 2：创建数据源
索引器从保存信息的 **数据源** （如连接字符串）拉取数据。 当前支持以下数据源：

* [Azure SQL 数据库或 Azure 虚拟机上的 SQL Server](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Azure Cosmos DB](search-howto-index-documentdb.md)
* [Azure Blob 存储](search-howto-indexing-azure-blob-storage.md)，用于提取 PDF、Office 文档、HTML 或 XML 中的文本
* [Azure 表存储](search-howto-indexing-azure-tables.md)

数据源的配置和管理独立于使用数据源的索引器，这意味着多个索引器可使用一个数据源，同时加载多个索引。

### <a name="step-3create-and-schedule-the-indexer"></a>步骤 3：创建和计划索引器
索引器定义是一种构造，用于指定索引、数据源和计划。 索引器可从另一个服务引用数据源，前提是该数据源来自同一个订阅。 有关构建索引器的详细信息，请参阅 [创建索引器（Azure 搜索 REST API）](https://msdn.microsoft.com/library/azure/dn946899.aspx)。

## <a name="next-steps"></a>后续步骤
了解基本概念后，下一步是查看每种数据源特定的要求和任务。

* [Azure SQL 数据库或 Azure 虚拟机上的 SQL Server](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Azure Cosmos DB](search-howto-index-documentdb.md)
* [Azure Blob 存储](search-howto-indexing-azure-blob-storage.md)，用于提取 PDF、Office 文档、HTML 或 XML 中的文本
* [Azure 表存储](search-howto-indexing-azure-tables.md)
* [使用 Azure 搜索 Blob 索引器为 CSV blob 编制索引](search-howto-index-csv-blobs.md)
* [使用 Azure 搜索 Blob 索引器为 JSON blob 编制索引](search-howto-index-json-blobs.md)

