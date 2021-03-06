---
redirect_url: https://azure.microsoft.com/services/cosmos-db/
ROBOTS: NOINDEX, NOFOLLOW
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: b9c8ef0343259d5a0adb2cefa9063f9d86edbc3c
ms.contentlocale: zh-cn
ms.lasthandoff: 05/10/2017


---
# <a name="storage-and-predictable-performance-provisioning-in-azure-cosmos-db"></a>Azure Cosmos DB 中的存储和可预测性能的预配
Azure Cosmos DB 是完全托管的，面向可扩展文档的针对 JSON 文档的 NoSQL 数据库服务。 使用 Azure Cosmos DB，无需租用虚拟机、部署软件或监视数据库。 Azure Cosmos DB 由 Microsoft 工程师操作和持续监视，提供一流的可用性、性能和数据保护。  

可通过在 [Azure 门户](https://portal.azure.com/)中[创建数据库帐户](documentdb-create-account.md)并创建 [Azure Cosmos DB 集合和数据库](documentdb-create-collection.md)来开始使用 Azure Cosmos DB Azure Cosmos DB 数据库以固态硬盘 (SSD) 支持的存储和吞吐量的单位数来提供。 这些存储单位在创建数据库集合时进行预配，每个集合的保留吞吐量可随时增加或减少以满足应用程序的需要。

如果应用程序超出一个或多个集合的保留吞吐量，将按每个集合限制请求。 这意味着一些应用程序请求可能成功，而另一些则可能受限制。

本文概述了可用于管理容量和计划数据存储的资源和指标。

## <a name="database-account"></a>数据库帐户
作为 Azure 订阅者，可预配一个或多个 Azure Cosmos DB 数据库帐户，用于管理数据库资源。 每个订阅都与单个 Azure 订阅相关联。

可通过 [Azure 门户](documentdb-create-account.md)，或者使用 [ARM 模板或 Azure CLI](documentdb-automation-resource-manager-cli.md)创建 Azure Cosmos DB 帐户。

## <a name="databases"></a>数据库
一个 Azure Cosmos DB 数据库可以包含几乎无限量的文档存储空间，这些存储空间分组到各个集合中。 集合提供性能隔离 - 每个集合都设置有吞吐量，此吞吐量不会与同一数据库或帐户中的其他集合共享。 Azure Cosmos DB 数据库的大小是灵活的 - 从几个 GB 到几个 TB 的 SSD 支持的文档存储和预设吞吐量。 与传统的 RDBMS 数据库不同，Azure Cosmos DB 中的数据库不局限于一台计算机，而是可以跨越多个计算机或群集。  

使用 Azure Cosmos DB，可根据扩展应用程序的需求创建多个集合或数据库，或同时创建两者。 可通过 [Azure 门户](documentdb-create-database.md)或任意一个 [Azure Cosmos DB SDK](documentdb-dotnet-samples.md) 创建数据库。   

## <a name="database-collections"></a>数据库集合
每个 Azure Cosmos DB 数据库均可包含一个或多个集合。 集合是用于文档存储和处理的高可用的数据分区。 每个集合可以存储具有各种架构的文档。 Azure Cosmos DB 的自动索引和查询功能允许你轻松筛选并检索文档。 集合提供了文档存储和查询执行的范围。 集合还是它所包含的所有文档的事务域。 基于 Azure 门户中设置的值或通过 SDK 为集合分配吞吐量。

Azure Cosmos DB 自动将集合分区到一个或多个物理服务器。 创建集合时，你可以指定预配吞吐量（根据每秒的请求单位数）和分区键属性。 Azure Cosmos DB 使用此属性的值将文档分布于分区和路由请求（例如查询）之间。 分区键值还可作为存储过程和触发器的事务边界。 每个集合都有该集合特定的保留吞吐量，且不会与相同帐户中的其他集合共享。 因此，你可以在存储和吞吐量方面扩大你的应用程序。

可以通过 [Azure 门户](documentdb-create-collection.md)或任意一个 [DocumentDB SDK](documentdb-sdk-dotnet.md) 创建集合。   

## <a name="request-units-and-database-operations"></a>请求单位数和数据库操作
创建集合时，可以根据每秒的[请求单位 (RU) 数](documentdb-request-units.md)保留吞吐量。 与考虑和管理硬件资源不同的是，可以考虑将**请求单位**作为所需资源的单个措施，执行各种数据库操作和服务应用程序请求。 不管集合中存储的项数有多少，或者同时运行的并发请求数有多少，读取 1 KB 的文档都要使用 1 个 RU。 所有针对 DocumentDB 的请求，包括复杂的操作如 SQL 查询，都有一个可预测的 RU 值，该值可在开发时确定。 如果你知道用于支持你的应用程序的文档的大小以及每个操作（读取、写入和查询）的频率，那么你可以设置恰到好处的吞吐量来满足应用程序需求，并且随着性能需求的变化增大和减小数据库的规模。

每个集合可以预留每秒 100 的倍数请求单位的数据块的吞吐量，每秒从几百到几百万个请求单位。 可以在集合的整个生命周期内调整设置的吞吐量，以适应不断变化的应用程序的处理需求和访问模式。 有关详细信息，请参阅 [Azure Cosmos DB 性能级别](documentdb-performance-levels.md)。

> [!IMPORTANT]
> 集合是计费实体。 集合的成本由集合的设置的吞吐量（以每秒的请求单位数来衡量）和总占用存储空间（以千兆字节为单位）决定。
>
>

一个特定操作如插入、删除、查询或存储过程执行会消耗多少个请求单位？ 请求单位是请求处理成本的规范化的度量。 读取 1 KB 文档需要 1 个 RU，但是插入、替换或删除同一文档的请求却要占用服务的更多处理，因此需要更多请求单位。 来自服务的每个响应包括一个自定义标头 (`x-ms-request-charge`)，其中报告了请求所要使用的请求单位数。 此标头也可通过 [SDK](documentdb-sdk-dotnet.md) 访问。 在 .NET SDK 中，[RequestCharge](https://msdn.microsoft.com/library/azure/dn933057.aspx#P:Microsoft.Azure.Documents.Client.ResourceResponse`1.RequestCharge) 是 [ResourceResponse](https://msdn.microsoft.com/library/azure/dn799209.aspx) 对象的属性。 如果要在进行单个调用前估计吞吐量需求，可以使用 [Capacity Planner](documentdb-request-units.md#estimating-throughput-needs) 来帮助进行此估计。

> [!NOTE]
> 用于 1 KB 文档的 1 个请求单位基准与使用[会话一致性](documentdb-consistency-levels.md)的简单的文档 GET 命令相对应。
>
>

有多种因素会影响针对某个 Azure Cosmos DB 数据库帐户的操作所使用的请求单位数。 这些因素包括：

* 文档大小。 随着文档大小的增加，用来读取或写入数据的单位数也随之增加。
* 属性计数。 假设默认为所有属性创建索引，用于写入文档的单位数将随着属性计数的增加而增加。
* 数据一致性。 当使用“强”或“有限过时”的数据一致性级别时，将占用更多单位数来读取文档。
* 已创建索引的属性。 每个集合的索引策略都可确定默认情况下要进行索引的属性类别。 通过限制已创建索引的属性的数量，可以减少请求单位的消耗。
* 文档索引。 默认情况下，将自动为每个文档创建索引，如果你选择不为其中一些文档创建索引，则将占用更少的请求单位。

有关详细信息，请参阅 [Azure Cosmos DB 请求单位](documentdb-request-units.md)

例如，下表显示了三种不同的文档大小（1 KB、4 KB 和 64 KB）和两个不同的性能级别（500 次读取/秒 + 100 次写入/秒和 500 次读取/秒 + 500 次写入/秒）所要设置的请求单位数。 数据一致性配置为会话级别，索引策略设置为无。

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p><strong>文档大小</strong></p></td>
            <td valign="top"><p><strong>读取数/秒</strong></p></td>
            <td valign="top"><p><strong>写入数/秒</strong></p></td>
            <td valign="top"><p><strong>请求单位</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>1 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>100</p></td>
            <td valign="top"><p>(500 * 1) + (100 * 5) = 1,000 RU/秒</p></td>
        </tr>
        <tr>
            <td valign="top"><p>1 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>(500 * 5) + (100 * 5) = 3,000 RU/秒</p></td>
        </tr>
        <tr>
            <td valign="top"><p>4 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>100</p></td>
            <td valign="top"><p>(500 * 1.3) + (100 * 7) = 1,350 RU/秒</p></td>
        </tr>
        <tr>
            <td valign="top"><p>4 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>(500 * 1.3) + (500 * 7) = 4,150 RU/秒</p></td>
        </tr>
        <tr>
            <td valign="top"><p>64 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>100</p></td>
            <td valign="top"><p>(500 * 10) + (100 * 48) = 9,800 RU/秒</p></td>
        </tr>
        <tr>
            <td valign="top"><p>64 KB</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>(500 * 10) + (500 * 48) = 29,000 RU/秒</p></td>
        </tr>
    </tbody>
</table>

查询、存储过程和触发器是根据所执行的操作的复杂性来使用请求单位的。 在开发应用程序时，检查请求费用标头，以更好地了解每个操作消耗请求单位容量的方式。  

## <a name="choice-of-consistency-level-and-throughput"></a>一致性级别的选择和吞吐量
默认一致性级别的选择会影响吞吐量和延迟。 可以编程方式和通过 Azure 门户设置默认的一致性级别。 你还可以重写单个请求的一致性级别。 默认情况下一致性级别设置为**会话**，该级别提供单调读取/写入和读取最新写入内容的保证。 会话一致性非常适用于以用户为中心的应用程序，可提供一致性和性能的完美平衡。    

有关在 Azure 门户上更改一致性级别的说明，请参阅[如何管理 Azure Cosmos DB 帐户](documentdb-manage-account.md#consistency)。 或者，有关一致性级别的详细信息，请参阅[使用一致性级别](documentdb-consistency-levels.md)。

## <a name="provisioned-document-storage-and-index-overhead"></a>设置的文档存储和索引开销
Azure Cosmos DB 支持创建单个分区和已分区的集合。 Azure Cosmos DB 中的每个分区支持高达 10 GB 有 SSD 支持的存储空间。 10 GB 文档存储空间包含文档和索引的存储。 默认情况下，Azure Cosmos DB 集合配置为自动为所有文档创建索引，且没有明确要求任何二级索引或架构。 根据使用 Azure Cosmos DB 的应用程序，索引开销通常介于 2-20%。 Azure Cosmos DB 使用的索引技术可以确保无论属性值是多少，索引开销都不会超过具有默认设置的文档大小的 80% 以上。

默认情况下，Azure Cosmos DB 为所有文档自动创建索引。 但是，如果要精细调整索引开销，则可在插入或替换文档时选择不对某些文档创建索引，如 [Azure Cosmos DB 索引策略](documentdb-indexing-policies.md)中所述。 可将 Azure Cosmos DB 集合配置为不对集合中的所有文档创建索引。 还可以将 Azure Cosmos DB 集合配置为有选择地只对 JSON 文档的某些具有通配符的属性或路径创建索引，如[配置集合的索引策略](documentdb-indexing-policies.md#CustomizingIndexingPolicy)中所述。 不对某些属性或文档创建索引还可以提高写入吞吐量 - 这意味着将消耗更少的请求单位。   

## <a name="next-steps"></a>后续步骤
若要继续学习 Azure Cosmos DB 的工作原理，请参阅 [Azure Cosmos DB 中的分区和缩放](documentdb-partition-data.md)。

有关在 Azure 门户中监视性能级别的说明，请参阅[监视 Azure Cosmos DB 帐户](documentdb-monitor-accounts.md)。 有关选择集合的性能级别的详细信息，请参阅 [Azure Cosmos DB 中的性能级别](documentdb-performance-levels.md)。

