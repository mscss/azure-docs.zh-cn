---
title: "使用 Livy Spark 向 Azure HDInsight 上的 Spark 群集提交作业 | Microsoft Docs"
description: "了解如何使用 Apache Spark REST API 将 Spark 作业远程提交到 Azure HDInsight 群集。"
keywords: apache spark rest api,livy spark
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 2817b779-1594-486b-8759-489379ca907d
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2017
ms.author: nitinme
ms.translationtype: Human Translation
ms.sourcegitcommit: e7da3c6d4cfad588e8cc6850143112989ff3e481
ms.openlocfilehash: 541aeb4eba6d00f13021af5789cf1dde961301fd
ms.contentlocale: zh-cn
ms.lasthandoff: 05/16/2017


---
# <a name="use-apache-spark-rest-api-to-submit-remote-jobs-to-an-hdinsight-spark-cluster"></a>使用 Apache Spark REST API 将远程作业提交到 HDInsight Spark 群集

了解如何使用 Livy，这是 Apache Spark REST API，用来将远程作业提交到 Azure HDInsight Spark 群集。 有关详细文档，请参阅 [Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server)。

你可以使用 Livy 运行交互式 Spark shell，或提交要在 Spark 上运行的批处理作业。 本文介绍如何使用 Livy 提交批处理作业。 以下语法使用 Curl 向Livy Spark 终结点发出 REST API 调用。

**先决条件：**

必须满足以下条件：

* HDInsight 上的 Apache Spark 群集。 有关说明，请参阅[在 Azure HDInsight 中创建 Apache Spark 群集](hdinsight-apache-spark-jupyter-spark-sql.md)。

## <a name="submit-a-livy-spark-batch-job"></a>提交 Livy Spark 批处理作业
在提交批处理作业之前，必须将应用程序 jar 上载到与群集关联的群集存储。 可以使用命令行实用工具 [**AzCopy**](../storage/storage-use-azcopy.md) 来执行此操作。 可以使用其他许多客户端来上载数据。 有关详细信息，请参阅[在 HDInsight 中上传 Hadoop 作业的数据](hdinsight-upload-data.md)。

    curl -k --user "<hdinsight user>:<user password>" -v -H <content-type> -X POST -d '{ "file":"<path to application jar>", "className":"<classname in jar>" }' 'https://<spark_cluster_name>.azurehdinsight.net/livy/batches'

**示例**：

* 如果 jar 文件位于群集存储 (WASB) 中
  
        curl -k --user "admin:mypassword1!" -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasbs://mycontainer@mystorageaccount.blob.core.windows.net/data/SparkSimpleTest.jar", "className":"com.microsoft.spark.test.SimpleFile" }' "https://mysparkcluster.azurehdinsight.net/livy/batches"
* 如果想要传递 jar 文件名和类名作为输入文件（在本示例中为 input.txt）的一部分
  
        curl -k  --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

## <a name="get-information-on-livy-spark-batches-running-on-the-cluster"></a>获取在群集上运行的 Spark 批处理的相关信息
    curl -k --user "<hdinsight user>:<user password>" -v -X GET "https://<spark_cluster_name>.azurehdinsight.net/livy/batches"

**示例**：

* 如果想要检索在群集上运行的所有Livy Spark 批处理：
  
        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches"
* 如果想要检索具有给定 batchId 的特定批
  
        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches/{batchId}"

## <a name="delete-a-livy-spark-batch-job"></a>删除 Livy Spark 批处理作业
    curl -k --user "<hdinsight user>:<user password>" -v -X DELETE "https://<spark_cluster_name>.azurehdinsight.net/livy/batches/{batchId}"

**示例**：

    curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/{batchId}"

## <a name="livy-spark-and-high-availability"></a>Livy Spark 和高可用性
Livy 可为群集上运行的 Spark 作业提供高可用性。 下面是一些示例。

* 将作业远程提交到 Spark 群集之后，如果 Livy 服务关闭，作业将继续在后台运行。 当 Livy 恢复运行时，将还原并报告作业的状态。
* 适用于 HDInsight 的 Jupyter 笔记本由后端中的 Livy 提供支持。 如果在笔记本运行 Spark 作业时重新启动 Livy 服务，笔记本将继续运行代码单元。 

## <a name="show-me-an-example"></a>举个例子
在本部分中，我们通过示例了解了如何使用 Livy Spark 提交批处理作业、监视作业进度，然后删除作业。 本示例中使用的应用程序是[创建独立的 Scala 应用程序并在 HDInsight Spark 群集上运行](hdinsight-apache-spark-create-standalone-application.md)一文中开发的应用程序。 以下步骤假设：

* 已将应用程序 jar 复制到与群集关联的存储帐户。
* 已将 CuRL 安装在用于执行这些步骤的计算机上。

执行以下步骤。

1. 让我们首先验证 Livy Spark 是否正在群集上运行。 为此，我们可以获取正在运行的批的列表。 如果这是你第一次使用 Livy 运行作业，则应会返回零。
   
        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches"
   
    你应会看到类似于下面的输出：
   
        < HTTP/1.1 200 OK
        < Content-Type: application/json; charset=UTF-8
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Fri, 20 Nov 2015 23:47:53 GMT
        < Content-Length: 34
        <
        {"from":0,"total":0,"sessions":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
   
    请注意输出中的最后一行显示为 **total:0**，这意味着未运行任何批。

2. 现在，让我们提交批处理作业。 以下代码片段使用输入文件 (input.txt) 传递 jar 名称和类名称作为参数。 如果要从 Windows 计算机执行这些步骤，则建议采用此方法。
   
        curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"
   
    文件 **input.txt** 中的参数定义如下：
   
        { "file":"wasbs:///example/jars/SparkSimpleApp.jar", "className":"com.microsoft.spark.example.WasbIOTest" }
   
    你应该会看到与下面类似的输出：
   
        < HTTP/1.1 201 Created
        < Content-Type: application/json; charset=UTF-8
        < Location: /0
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Fri, 20 Nov 2015 23:51:30 GMT
        < Content-Length: 36
        <
        {"id":0,"state":"starting","log":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
   
    请注意输出的最后一行显示为 **state:starting**。 此外显示了 **id:0**。 这是批 ID。

3. 现在，可以使用批 ID 来检索此特定批的状态。
   
        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches/0"
   
    你应该会看到与下面类似的输出：
   
        < HTTP/1.1 200 OK
        < Content-Type: application/json; charset=UTF-8
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Fri, 20 Nov 2015 23:54:42 GMT
        < Content-Length: 509
        <
        {"id":0,"state":"success","log":["\t diagnostics: N/A","\t ApplicationMaster host: 10.0.0.4","\t ApplicationMaster RPC port: 0","\t queue: default","\t start time: 1448063505350","\t final status: SUCCEEDED","\t tracking URL: http://hn0-myspar.lpel1gnnvxne3gwzqkfq5u5uzh.jx.internal.cloudapp.net:8088/proxy/application_1447984474852_0002/","\t user: root","15/11/20 23:52:47 INFO Utils: Shutdown hook called","15/11/20 23:52:47 INFO Utils: Deleting directory /tmp/spark-b72cd2bf-280b-4c57-8ceb-9e3e69ac7d0c"]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
   
    现在，输出显示 **state:success**，这意味着作业已成功完成。

4. 现在，可以根据需要删除该批。
   
        curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/0"
   
    你应该会看到与下面类似的输出：
   
        < HTTP/1.1 200 OK
        < Content-Type: application/json; charset=UTF-8
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Sat, 21 Nov 2015 18:51:54 GMT
        < Content-Length: 17
        <
        {"msg":"deleted"}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
   
    输出的最后一行显示批已成功删除。 如果删除正在运行的作业，实际上是终止该作业。 如果删除已完成的作业，则不管该作业是否已成功完成，都将完全删除该作业的信息。

## <a name="using-livy-spark-on-hdinsight-35-clusters"></a>在 HDInsight 3.5 群集上使用 Livy Spark

HDInsight 3.5 群集默认情况下禁止使用本地文件路径访问示例数据文件或 jar。 建议改用 `wasb://` 路径访问群集中的 jar 或示例数据文件。 如果要使用本地路径，则必须相应地更新 Ambari 配置。 为此，请执行以下操作：

1. 转到群集的 Ambari 门户。 Ambari Web UI 在 HDInsight 群集上提供，网址为 https://**CLUSTERNAME**.azurehdidnsight.net，其中 CLUSTERNAME 是群集的名称。

2. 在左侧导航中，单击“Livy”，然后单击“配置”。

3. 如果要允许完全访问文件系统，请在 **livy-default** 下添加属性名称 `livy.file.local-dir-whitelist`，并将其值设置为 **"/"**。 如果要仅允许访问特定目录，请提供该目录的路径作为值。

## <a name="troubleshooting"></a>故障排除

以下是使用 Livy 将远程作业提交到 Spark 群集时可能会遇到的一些问题。

### <a name="using-an-external-jar-from-the-additional-storage-is-not-supported"></a>不支持从附加存储使用外部 jar

**问题：**如果正在运行 Livy Spark 作业并引用了与群集关联的附加存储中的外部 jar，则作业将失败。

**解决方法：**请确保要使用的 jar 在与 HDInsight 群集关联的默认存储中可用。


## <a name="seealso"></a>另请参阅
* [概述：Azure HDInsight 上的 Apache Spark](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>方案
* [Spark 和 BI：使用 HDInsight 中的 Spark 和 BI 工具执行交互式数据分析](hdinsight-apache-spark-use-bi-tools.md)
* [Spark 和机器学习：使用 HDInsight 中的 Spark 对使用 HVAC 数据生成温度进行分析](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark 和机器学习：使用 HDInsight 中的 Spark 预测食品检查结果](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Spark 流式处理：使用 HDInsight 中的 Spark 生成实时流式处理应用程序](hdinsight-apache-spark-eventhub-streaming.md)
* [使用 HDInsight 中的 Spark 分析网站日志](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>创建和运行应用程序
* [使用 Scala 创建独立的应用程序](hdinsight-apache-spark-create-standalone-application.md)

### <a name="tools-and-extensions"></a>工具和扩展
* [使用适用于 IntelliJ IDEA 的 HDInsight 工具插件创建和提交 Spark Scala 应用程序](hdinsight-apache-spark-intellij-tool-plugin.md)
* [Use HDInsight Tools Plugin for IntelliJ IDEA to debug Spark applications remotely（使用 IntelliJ IDEA 的 HDInsight 工具插件远程调试 Spark 应用程序）](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [在 HDInsight 上的 Spark 群集中使用 Zeppelin 笔记本](hdinsight-apache-spark-use-zeppelin-notebook.md)
* [在 HDInsight 的 Spark 群集中可用于 Jupyter 笔记本的内核](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Use external packages with Jupyter notebooks（将外部包与 Jupyter 笔记本配合使用）](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Install Jupyter on your computer and connect to an HDInsight Spark cluster（在计算机上安装 Jupyter 并连接到 HDInsight Spark 群集）](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>管理资源
* [管理 Azure HDInsight 中 Apache Spark 群集的资源](hdinsight-apache-spark-resource-manager.md)
* [Track and debug jobs running on an Apache Spark cluster in HDInsight（跟踪和调试 HDInsight 中的 Apache Spark 群集上运行的作业）](hdinsight-apache-spark-job-debugging.md)


