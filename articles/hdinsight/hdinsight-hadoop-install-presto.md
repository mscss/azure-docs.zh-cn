---
title: "在 Azure HDInsight Linux 群集上安装 Presto | Microsoft Docs"
description: "了解如何使用脚本操作在基于 Linux 的 HDInsight Hadoop 群集上安装 Presto 和 Airpal。"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/17/2017
ms.author: nitinme
ms.translationtype: Human Translation
ms.sourcegitcommit: 95b8c100246815f72570d898b4a5555e6196a1a0
ms.openlocfilehash: 406ef84e72d253fec51a0b37c48f326dafd511b6
ms.contentlocale: zh-cn
ms.lasthandoff: 05/18/2017


---
# <a name="install-and-use-presto-on-hdinsight-hadoop-clusters"></a>在 HDInsight Hadoop 群集上安装并使用 Presto

本主题介绍如何使用脚本操作在 HDInsight Hadoop 群集上安装 Presto。 此外，还介绍如何在现有的 Presto HDInsight 群集上安装 Airpal。

> [!IMPORTANT]
> 本文档中的步骤需要使用 Linux 的 **HDInsight 3.5 Hadoop 群集**。 Linux 是 HDInsight 3.4 或更高版本上使用的唯一操作系统。 有关详细信息，请参阅 [HDInsight 版本](hdinsight-component-versioning.md)。

## <a name="what-is-presto"></a>什么是 Presto？
[Presto](https://prestodb.io/overview.html) 是适用于大数据的快速分布式 SQL 查询引擎。 Presto 适合用于对 PB 量级的数据进行交互式查询。 有关 Presto 的组件及其如何配合工作的详细信息，请参阅 [Presto 的概念](https://github.com/prestodb/presto/blob/master/presto-docs/src/main/sphinx/overview/concepts.rst)。

> [!WARNING]
> 完全支持通过 HDInsight 群集提供的组件，Microsoft 支持部门将帮助你找出并解决与这些组件相关的问题。
> 
> 自定义组件（如 Presto）可获得合理范围的支持，以帮助进一步排查问题。 这可能导致问题解决，或要求你参与可用的开放源代码技术渠道，在该处可找到该技术的深入专业知识。 有许多可以使用的社区站点，例如：[HDInsight 的 MSDN 论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight)和 [http://stackoverflow.com](http://stackoverflow.com)。 此外，Apache 项目在 [http://apache.org](http://apache.org) 上提供了项目站点，例如 [Hadoop](http://hadoop.apache.org/)。
> 
> 


## <a name="install-presto-using-script-action"></a>使用脚本操作安装 Presto

本部分提供有关如何在通过使用 Azure 门户创建新群集时使用示例脚本的说明。 

1. 使用[预配基于 Linux 的 HDInsight 群集](hdinsight-hadoop-create-linux-clusters-portal.md)中的步骤开始预配群集。 请确保使用**自定义**群集创建流创建群集。 必须确保创建的群集满足以下要求。

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 它必须是装有 HDInsight 3.5 的 Hadoop 群集。

    b. 它必须使用 Azure 存储作为数据存储。 目前不支持在将 Azure Data Lake Store 用作存储选项的群集上使用 Presto。 

    ![使用自定义选项创建 HDInsight 群集](./media/hdinsight-hadoop-install-presto/hdinsight-install-custom.png)

2. 在“高级设置”边栏选项卡上，选择“脚本操作”，并提供以下信息：
   
   * **名称**：输入脚本操作的友好名称。
   * **Bash 脚本 URI**：`https://raw.githubusercontent.com/hdinsight/presto-hdinsight/master/installpresto.sh`
   * **标头**：选中此选项
   * **辅助角色**：选中此选项
   * **ZOOKEEPER**：清除此复选框
   * **参数**：将此字段留空


3. 在“脚本操作”边栏选项卡底部，单击“选择”按钮保存配置。 最后，单击“高级设置”边栏选项卡底部的“选择”按钮保存配置信息。

4. 根据[预配基于 Linux 的 HDInsight 群集](hdinsight-hadoop-create-linux-clusters-portal.md)中所述继续预配群集。

    > [!NOTE]
    > Azure PowerShell、Azure CLI、HDInsight .NET SDK 或 Azure Resource Manager 模板也可用于应用脚本操作。 你也可以将脚本操作应用于已在运行的群集。 有关详细信息，请参阅[使用脚本操作自定义 HDInsight 群集](hdinsight-hadoop-customize-cluster-linux.md)。
    > 
    > 

## <a name="use-presto-with-hdinsight"></a>在 HDInsight 中使用 Presto

使用前面所述的步骤安装 Presto 后，请执行以下步骤在 HDInsight 群集中使用 Presto。

1. 使用 SSH 连接到 HDInsight 群集：
   
        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
   
    有关详细信息，请参阅 [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)（对 HDInsight 使用 SSH）。
     

2. 使用以下命令启动 Presto shell。
   
        presto --schema default

3. 针对所有 HDInsight 群集默认提供的示例表 **hivesampletable** 运行查询。
   
        select count (*) from hivesampletable;
   
    默认情况下，已配置适用于 Presto 的 [Hive](https://prestodb.io/docs/current/connector/hive.html) 和 [TPCH](https://prestodb.io/docs/current/connector/tpch.html) 连接器。 Hive 连接器配置为使用默认安装的 Hive 安装，因此 Hive 中的所有表将自动在 Presto 中显示。

    有关如何使用 Presto 的详细说明，请参阅 [Presto 文档](https://prestodb.io/docs/current/index.html)。

## <a name="use-airpal-with-presto"></a>将 Airpal 与 Presto 配合使用

[Airpal](https://github.com/airbnb/airpal#airpal) 是适用于 Presto 的基于 Web 的开源查询接口。 有关 Airpal 的详细信息，请参阅 [Airpal 文档](https://github.com/airbnb/airpal#airpal)。

本部分介绍在 Presto 已安装的 HDInsight Hadoop 群集边缘节点上**安装 Airpal** 的步骤。 这可确保能够通过 Internet 使用 Airpal Web 查询接口。

1. 使用 SSH 连接到 Presto 已安装的 HDInsight Hadoop 群集边缘节点：
   
        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
   
    有关详细信息，请参阅 [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)（对 HDInsight 使用 SSH）。

2. 连接后，运行以下命令。

        sudo slider registry  --name presto1 --getexp presto 
   
    你应该看到如下输出：

        {
              "coordinator_address" : [ {
                "value" : "10.0.0.12:9090",
                "level" : "application",
                "updatedTime" : "Mon Apr 03 20:13:41 UTC 2017"
          } ]

3. 在输出中，记下 **value** 属性的值。 在群集边缘节点上安装 Airpal 时需要用到它。 从上面的输出中，所需的值为 **10.0.0.12:9090**。

4. 使用**[此处](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhdinsight%2Fpresto-hdinsight%2Fmaster%2Fairpal-deploy.json)**的模板创建 HDInsight 群集边缘节点并提供值，如以下屏幕截图中所示。

    ![HDInsight 在 Presto 群集上安装 Airpal](./media/hdinsight-hadoop-install-presto/hdinsight-install-airpal.png)

5. 单击“购买”。

6. 将更改应用到群集配置后，可以使用以下步骤访问 Airpal Web 接口。

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在群集边栏选项卡中单击“应用程序”。

    ![HDInsight 在 Presto 群集上启动 Airpal](./media/hdinsight-hadoop-install-presto/hdinsight-presto-launch-airpal.png)

    b. 在“已安装的应用”边栏选项卡中，单击 Airpal 对应的“门户”。

    ![HDInsight 在 Presto 群集上启动 Airpal](./media/hdinsight-hadoop-install-presto/hdinsight-presto-launch-airpal-1.png)

    c. 出现提示时，请输入创建 HDInsight Hadoop 群集时指定的管理员凭据。

## <a name="customize-a-presto-installation-on-hdinsight-cluster"></a>自定义 HDInsight 群集上的 Presto 安装

在 HDInsight Hadoop 群集上安装 Presto 后，可以自定义安装，以进行各种更改，例如更新内存设置、更改连接器，等等。为此，请执行以下步骤。

1. 使用 SSH 连接到 Presto 已安装的 HDInsight Hadoop 群集边缘节点：
   
        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
   
    有关详细信息，请参阅 [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)（对 HDInsight 使用 SSH）。

2. 在 `/var/lib/presto/presto-hdinsight-master/appConfig-default.json` 文件中进行配置更改。 有关 Presto 配置的详细信息，请参阅[基于 YARN 的群集的 Presto 配置](https://prestodb.io/presto-yarn/installation-yarn-configuration-options.html)。

3. 停止并终止当前正在运行的 Presto 实例。

        sudo slider stop presto1 --force
        sudo slider destroy presto1 --force

4. 使用自定义启动 Presto 的新实例。

       sudo slider create presto1 --template /var/lib/presto/presto-hdinsight-master/appConfig-default.json --resources /var/lib/presto/presto-hdinsight-master/resources-default.json

5. 等到新实例准备就绪，然后记下 Presto 的协调器地址。


       sudo slider registry --name presto1 --getexp presto

## <a name="generate-benchmark-data-for-hdinsight-clusters-that-run-presto"></a>为运行 Presto 的 HDInsight 群集生成基准数据

TPC-DS 是有关测量多个决策支持系统（包括大数据系统）的性能的行业标准。 可以在 HDInsight 群集上使用 Presto 生成数据，并将它与你自己的 HDInsight 基准数据进行比较并评估结果。 有关详细信息，请参阅[此文](https://github.com/hdinsight/tpcds-datagen-as-hive-query/blob/master/README.md)。



## <a name="see-also"></a>另请参阅
* [在 HDInsight 群集上安装并使用 Hue](hdinsight-hadoop-hue-linux.md)。 Hue 是一个 Web UI，可让你轻松地创建、运行和保存 Pig 与 Hive 作业，以及浏览 HDInsight 群集的默认存储。

* [在 HDInsight 群集上安装 Giraph](hdinsight-hadoop-giraph-install-linux.md)。 使用群集自定义在 HDInsight Hadoop 群集上安装 Giraph。 Giraph 可让你通过使用 Hadoop 执行图形处理，并可以在 Azure HDInsight 上使用。

* [在 HDInsight 群集上安装 Solr](hdinsight-hadoop-solr-install-linux.md)。 使用群集自定义在 HDInsight Hadoop 群集上安装 Solr。 Solr 允许你对存储的数据执行功能强大的搜索操作。

[hdinsight-install-r]: hdinsight-hadoop-r-scripts-linux.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md

