---
title: "什么是 Azure 搜索 | Microsoft Docs"
description: "Azure 搜索是完全托管的托管云搜索服务。 通过此功能概述，了解详细信息。"
services: search
manager: jhubbard
author: ashmaka
documentationcenter: 
ms.assetid: 50bed849-b716-4cc9-bbbc-b5b34e2c6153
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 04/24/2017
ms.author: ashmaka
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: db227bfea10255322c090e68b197cfb2dd1cf15b
ms.contentlocale: zh-cn
ms.lasthandoff: 05/10/2017

---
# <a name="what-is-azure-search"></a>什么是 Azure 搜索？
Azure 搜索是一种搜索即服务云解决方案，它将服务器和基础结构的管理委托给 Microsoft，为用户提供随时可用的服务，用户可在填充数据后使用此服务将搜索添加到 Web 或移动应用程序。 借助 Azure 搜索，可以使用简单的 [REST API](/rest/api/searchservice/) 或 [.NET SDK](search-howto-dotnet-sdk.md) 在应用程序中添加稳定的搜索体验，无需管理搜索基础结构或精通搜索技术。

<a name="feature-drilldown"></a>

## <a name="embed-a-powerful-search-experience-in-your-app-or-site"></a>在应用或站点中嵌入功能强大的搜索体验 

了解 Azure 搜索中的功能。

### <a name="full-text-search-and-text-analysis"></a>全文搜索和文本分析

[全文搜索](https://en.wikipedia.org/wiki/Full_text_search)是大多数基于搜索的应用的主要用例。 在 Azure 搜索中，查询可以使用[简单的查询语法](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)进行表述，它提供逻辑运算符、短语搜索运算符、后缀运算符、优先级运算符。 此外，[Lucene 查询语法](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)可以启用模糊搜索、近似搜索、术语提升和正则表达式。 Azure 搜索还支持[自定义的词汇分析器](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search)，以允许应用程序使用拼音匹配和正则表达式处理复杂的搜索查询。

### <a name="language-support"></a>语言支持

Azure 搜索支持适用于 [56 种不同语言](https://docs.microsoft.com/rest/api/searchservice/language-support)的词汇分析器。 使用 Lucene 分析器和 Microsoft 分析器（Office 和必应中的自然语言处理已经过多年优化），Azure 搜索可以在应用程序的搜索框中分析文本，从而以智能方式处理特定于语言的语言学，包括谓词时态、词性、不规则复数名词（例如“mouse”与“mice”）、词取消复合、词拆分（对于不带空格的语言）等。

### <a name="data-integration"></a>数据集成

你可以推送 JSON 数据结构来填充 Azure 搜索索引。 另外，对于受支持的数据源，可以使用[索引器](search-indexer-overview.md)自动爬网式搜索 [Azure SQL 数据库](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)、[Azure Cosmos DB](search-howto-index-documentdb.md) 或 [Azure Blob 存储](search-howto-indexing-azure-blob-storage.md)，以便将搜索索引的内容与主要数据存储同步。

*文档破解*实现了[主要文件格式的索引编制](search-howto-indexing-azure-blob-storage.md)，包括 Microsoft Office、PDF 和 HTML 文档。

### <a name="search-experience"></a>搜索体验

+ 可针对自动完成搜索栏和提前键入查询启用**搜索建议**。 当用户输入部分搜索输入内容时，[将显示索引中实际文档的建议](https://docs.microsoft.com/rest/api/searchservice/suggesters)。

+ 通过[单个查询参数](https://docs.microsoft.com/azure/search/search-faceted-navigation)实现了**分面导航**。 Azure 搜索返回一个分面导航结构，你可以将该结构用作类别列表背后的代码，用于自定向筛选（例如，按价格范围或品牌来筛选目录项）。

+ 可以使用**筛选器**将分面导航纳入到应用程序的 UI 中，改进查询表述，以及基于用户或开发人员指定的条件进行筛选。 可以使用 [OData 语法](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search)创建筛选器。

+ **命中项突出显示**[向搜索结果中的匹配关键字应用可视化格式设置](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)。 可以选择哪些字段返回突出显示的片段。

+ **简单计分**是 Azure 搜索的主要优点。 [计分配置文件](/rest/api/searchservice/add-scoring-profiles-to-a-search-index)用于在文档中自行将相关性建模为值的函数。 例如，你可能希望较新产品或打折产品显示在搜索结果的顶部位置。 也可以基于已跟踪和单独存储的客户搜索首选项将标记用于个性化计分，来生成计分配置文件。

+ **排序**通过索引架构提供给多个字段，然后使用单个搜索参数在查询时切换。

+ [通过 Azure 搜索所提供的对搜索结果的优化控制](search-pagination-page-layout.md)，**分页**和限制搜索结果将变得更简单。  

### <a name="geosearch"></a>地理搜索

Azure 搜索可以智能地处理、筛选和显示地理位置。 它可以让用户基于搜索结果与物理位置的临近程度浏览数据。 本视频介绍其工作原理：[第 9 频道：Azure 搜索和地理空间数据](https://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data)。

### <a name="monitoring-and-reporting"></a>监视和报告

+ [可以收集并分析](search-traffic-analytics.md)**搜索流量分析**，以便根据用户键入到搜索框的内容来解锁见解。

+ 门户页面中会捕获并报告关于每秒查询数、延迟和限制的指标，无需额外进行配置。 你还可以轻松监视索引和文档计数，以便可以根据需要调整容量。 

### <a name="tools-for-prototyping-and-inspection"></a>用于原型制作和检查的工具

在门户中，可以使用**导入数据**向导来配置索引器、索引设计器以建立索引，并可以使用**搜索浏览器**来测试查询并优化评分配置文件。 还可以打开任何索引来查看其架构。

<a name="cloud-service-advantage"></a>

### <a name="cloud-service-advantages"></a>云服务的优点

+ **高可用性**可确保极其可靠的搜索服务体验。 正确缩放时，[Azure 搜索可提供 99.9% SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/)。

+ 作为一种**完全托管且可缩放的**端到端解决方案，Azure 搜索绝对不需要基础结构管理。 通过在两个维度进行缩放以便处理更多文档存储和/或更高的查询负载，可以根据你的需求来定制服务。

## <a name="how-it-works"></a>工作原理
### <a name="step-1-provision-service"></a>步骤 1：预配服务
可以通过 [Azure 门户](https://portal.azure.com/)或 [Azure 资源管理 API](/rest/api/searchmanagement/) 调整 Azure 搜索服务。 可以选择与其他订阅者共享的免费服务，或者服务专用的资源[付费层](https://azure.microsoft.com/pricing/details/search/)。

对于付费层，可朝两个维度缩放服务： 

- 添加副本以增长容量来处理重型查询负载。   
- 添加分区以便为更多文档增加存储。 

通过单独处理文档存储和查询吞吐量，可以根据生产要求进行资源调配。

### <a name="step-2-create-index"></a>步骤 2：创建索引
上传可搜索的内容之前，必须先定义 Azure 搜索索引。 索引类似于用于保存数据的数据库表，可接受搜索查询。 定义要映射的索引架构，以反映要搜索的文档结构，这类似于数据库中的字段。

架构可在 Azure 门户中创建，也可以[使用 .NET SDK](search-howto-dotnet-sdk.md) 或 [REST API](/rest/api/searchservice/) 以编程方式创建。

### <a name="step-3-index-data"></a>步骤 3：索引数据
定义索引后，便可以上传内容。 可以使用推送或提取模型。

提取模型从外部数据源检索数据。 支持通过*索引器*检索数据。索引器可以简化和自动数据引入的方方面面，例如，连接、读取和序列化数据。 [索引器](/rest/api/searchservice/Indexer-operations)适用于 Azure Cosmos DB、Azure SQL 数据库、Azure Blob 存储，以及 Azure VM 中托管的 SQL Server。 可以针对按需刷新或计划的数据刷新配置索引器。

推模型通过 SDK 或 REST API 进行提供，用于将更新的文档发送到索引。 可以从使用 JSON 格式的几乎任何数据集推送数据。 有关加载数据的指南，请参阅[添加、更新或删除文档](/rest/api/searchservice/addupdate-or-delete-documents)或[如何使用.NET SDK）](search-howto-dotnet-sdk.md)。

### <a name="step-4-search"></a>步骤 4：搜索
填充索引后，可以通过将简单的 HTTP 请求与 REST API 或 .NET SDK 结合使用，向服务终结点[发出搜索查询](/rest/api/searchservice/Search-Documents)。

## <a name="how-it-compares"></a>它如何进行比较

客户经常询问，如何在 [Azure 搜索中的全文搜索](search-lucene-query-architecture.md)与数据库产品中的[全文搜索](https://en.wikipedia.org/wiki/Full_text_search)之间做出比较。 我们的回答是，Azure 搜索语言功能更丰富且更灵活，支持 Lucene 查询、Lucene 和 Microsoft 提供的语言分析器、用于拼音或其他专用输入的自定义分析器，并可以在搜索索引中合并来自多个源的数据。 

另一个要点在于，搜索解决方案必须能够满足整个搜索体验的需求。 例如，你可能希望获得自定义的结果评分、自我定向式筛选的分面导航、搜索词突出显示和联想式的查询建议。 

用于监视和了解查询活动的工具也可能会成为搜索解决方案决策的考虑因素。 例如，Azure 搜索支持对点击率、最热门搜索、无点击搜索等指标执行[搜索流量分析](search-traffic-analytics.md)。 在搜索属于加载项的产品中，可能找不到这些功能。

资源利用率是另一个考虑因素。 自然语言搜索通常会消耗大量计算资源。 我们的某些客户已将搜索操作负载转移到 Azure 搜索，目的是腾出计算机资源用于事务处理。 通过将搜索外部化，可以根据查询量轻松调整规模。

决定使用专用搜索后，下一步就是在云服务与本地服务器之间做出抉择。 如果想要获得一个[开销和维护工作量极少且规模可调的周全解决方案](#cloud-service-advantage)，则云服务是适当的选择。

在云的范式中，许多提供程序提供相当的基线功能，以及全文搜索、地理搜索，并且能够处理搜索输入中一定程度的模糊性。 通常，它是一项[专用功能](#feature-drilldown)，或者是 API、工具以及用于确定最匹配项的管理功能的易化和总体简化。

在所有云提供程序中，对于主要依赖于信息检索搜索和内容导航的应用，Azure 搜索在处理 Azure 上的内容存储和数据库的全文搜索工作负荷方面最为强大。 主要优势包括：

+ 在索引层的 Azure 数据集成（爬网程序）
+ 用于集中管理的 Azure 门户
+ Azure 可伸缩性、可靠性和世界一流的可用性
+ 语言分析和自定义分析，提供分析器，用于支持以 56 种语言进行可靠的全文搜索
+ [对以搜索为中心的应用通用的核心功能](#feature-drilldown)：评分、分面、建议、同义词、地理搜索，等等。

> [!Note]
> 明确地说，非 Azure 数据源完全受支持，但依赖于代码密集程度更高的推送方法而不是索引器。 使用我们的 API 可以通过管道将任何 JSON 文档集合传输到 Azure 搜索引擎。

在我们的所有客户中，能够利用 Azure 搜索中最广泛功能的客户包括在线目录、业务线程序以及文档发现应用程序。

## <a name="rest-api--net-sdk"></a>REST API | .Net SDK

虽然可以在门户中执行许多任务，但 Azure 搜索是为希望将搜索功能集成到现有应用程序中的开发人员打造的。 可以使用以下编程接口。

|平台 |说明 |
|-----|------------|
|[REST](/rest/api/searchservice/) | 任何编程平台和语言（包括 Xamarin、Java 和 JavaScript）支持的 HTTP 命令|
|[.NET SDK](search-howto-dotnet-sdk.md) | REST API 的 .NET 包装器以 C# 和其他针对 .NET Framework 的托管代码语言提供了有效编码。 |

## <a name="free-trial"></a>免费试用
Azure 订户可以[在免费层中预配服务](search-create-service-portal.md)。

如果你不是订户，可以[免费建立一个 Azure 帐户](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)。 你将获得试用付费版 Azure 服务的信用额度。 信用额度用完后，可以保留该帐户并继续使用[免费的 Azure 服务](https://azure.microsoft.com/free/)。 除非你显式更改设置并要求付费，否则不会对你的信用卡收取任何费用。

也可以[激活 MSDN 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)：MSDN 订阅每月为你提供可用于试用付费版 Azure 服务的信用额度。 

## <a name="watch-a-short-video"></a>观看短视频

搜索引擎是在移动应用中、网站上和公司数据存储中检索信息时常用的驱动程序。 Azure 搜索提供了用于打造与大型商业网站上的搜索体验类似的搜索体验的工具。

这段时长为 9 分钟的视频出自项目经理 Liam Cavanagh 之手，其中介绍了集成搜索引擎如何让应用受益。 简短的演示介绍了 Azure 搜索中的主要功能以及典型的工作流。 

>[!VIDEO https://channel9.msdn.com/Events/Connect/2016/138/player]
 
+ 0-3 分钟介绍了主要功能和用例。
+ 3-4 分钟介绍了服务预配。 
+ 4-6 分钟介绍了用来使用内置的房地产业数据集创建索引的“导入数据”向导。
+ 6-9 分钟介绍了搜索浏览器和各种查询。



