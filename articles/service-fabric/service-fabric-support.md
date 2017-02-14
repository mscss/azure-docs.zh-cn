---
title: "Azure Service Fabric 支持选项 | Microsoft Docs"
description: "支持的 Azure Service Fabric 群集版本，以及文件支持票证的链接。"
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/07/2016
ms.author: chackdan
translationtype: Human Translation
ms.sourcegitcommit: d848bebea962e8ba883266188cd0bafe991dd804
ms.openlocfilehash: 5d27e6622faeee7cf9f4cdb2911171ef29d94b5c


---
# <a name="azure-service-fabric-support-options"></a>Azure Service Fabric 支持选项

我们为用户设置了各种选项，方便其为 Service Fabric 群集（在其上运行应用程序工作负荷）提供相应的支持。 用户需根据所需支持级别以及问题的严重性，选取适当的选项。 


<a id="getlivesitesupportonazure"></a>

## <a name="report-production-or-live-site-issues-or-request-paid-support-for-azure"></a>报告生产或实时站点问题，或者请求 Azure 付费支持

若要报告部署在 Azure 上的 Service Fabric 群集的实时站点问题，请通过 [Azure 门户](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)或 [Microsoft 支持门户](http://support.microsoft.com/oas/default.aspx?prid=16146)开具专业支持票证。

了解有关以下方面的详细信息：
 
- [Microsoft 提供的 Azure 专业支持](https://azure.microsoft.com/en-us/support/plans/?b=16.44)。
- [Microsoft 顶级支持](https://support.microsoft.com/en-us/premier)。

<a id="getlivesitesupportonprem"></a>

## <a name="report-production-or-live-site-issues-or-request-paid-support-for-standalone-service-fabric-clusters"></a>报告生产或实时站点问题，或者请求独立 Service Fabric 群集的付费支持

若要报告部署在本地或其他云上的 Service Fabric 群集的实时站点问题，请通过 [Microsoft 支持门户](http://support.microsoft.com/oas/default.aspx?prid=16146)开具专业支持票证。

了解有关以下方面的详细信息：

- [Microsoft 提供的本地部署专业支持](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0)。
- [Microsoft 顶级支持](https://support.microsoft.com/en-us/premier)。


<a id="getsupportonissues"></a>
## <a name="report-azure-service-fabric-issues"></a>报告 Azure Service Fabric 问题 
我们已设置 GitHub 存储库，用于报告 Service Fabric 问题。  我们还积极监视以下论坛。

### <a name="github-repo"></a>GitHub 存储库 
在 [Service-Fabric-issues git 存储库](https://github.com/Azure/service-fabric-issues)中报告 Azure Service Fabric 问题。 此存储库用于报告和跟踪 Azure Service Fabric 问题，以及进行小型功能请求。 **请勿使用此功能报告实时站点问题**。

### <a name="stackoverflow-and-msdn-forums"></a>StackOverflow 和 MSDN 论坛

[StackOverflow 上的 Service Fabric 标记][stackoverflow]和 [MSDN 上的 Service Fabric 论坛][msdn-forum]最适合提问有关平台工作方式以及如何通过该平台完成某些任务的问题。

### <a name="azure-feedback-forum"></a>Azure 反馈论坛

[有关 Service Fabric 的 Azure 反馈论坛][uservoice-forum]最适合提交用户关于产品的大型功能创意，我们可以看到，大多数常见的请求都属于我们的中长期规划。 我们鼓励你在社区内争取大家对你的建议的支持。


<a id="releasesuport"></a>
## <a name="supported-service-fabric-versions"></a>支持的 Service Fabric 版本。

确保群集始终运行支持的 Service Fabric 版本。 宣布发行新版 Service Fabric 标志着自该日期起至少 60 天以后结束对旧版本的支持。 新版本[在 Service Fabric 团队博客](https://blogs.msdn.microsoft.com/azureservicefabric/)上公布。

请参阅以下文档，详细了解如何才能让群集始终运行支持的 Service Fabric 版本。

- [在 Azure 群集上升级 Service Fabric 版本](service-fabric-cluster-upgrade.md)
- [在单独的 Windows Server 群集上升级 Service Fabric 版本](service-fabric-cluster-upgrade-windows-server.md)
 
下面是支持的 Service Fabric 版本的列表以及支持结束日期。

| **Service Fabric 运行时群集** | **支持结束日期** |
| --- | --- |
| 5.3.121 之前的所有群集版本 |2017 年 1 月 20 日 |
| 5.3.121.* |2017 年 2 月 24 日 |
| 5.3.204.* |2017 年 2 月 24 日 |
| 5.3.301.* |2017 年 2 月 24 日 |
| 5.3.311.* |2017 年 2 月 24 日 |
| 5.4. *. * |最新版本，因此尚无结束日期 |


## <a name="next-steps"></a>后续步骤

- [在 Azure 群集上升级 Service Fabric 版本](service-fabric-cluster-upgrade.md)
- [在单独的 Windows Server 群集上升级 Service Fabric 版本](service-fabric-cluster-upgrade-windows-server.md)

<!--references-->
[msdn-forum]: https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureServiceFabric
[stackoverflow]: http://stackoverflow.com/questions/tagged/azure-service-fabric
[uservoice-forum]: https://feedback.azure.com/forums/293901-service-fabric
[acom-docs]: http://aka.ms/servicefabricdocs
[sample-repos]: http://aka.ms/servicefabricsamples



<!--HONumber=Dec16_HO2-->

